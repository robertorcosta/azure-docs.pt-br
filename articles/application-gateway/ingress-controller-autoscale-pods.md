---
title: Pods AKS de escala automática com métricas do Gateway de aplicativo do Azure
description: Este artigo fornece instruções sobre como dimensionar seus pods back-end AKS usando métricas do Application Gateway e adaptador métrico do Azure Kubernetes
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239442"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Dimensione automaticamente seus pods AKS usando o Application Gateway Metrics (Beta)

À medida que o tráfego de entrada aumenta, torna-se crucial aumentar seus aplicativos com base na demanda.

No tutorial a seguir, explicamos como você `AvgRequestCountPerHealthyHost` pode usar a métrica do Application Gateway para escalar seu aplicativo. `AvgRequestCountPerHealthyHost`mede as solicitações médias enviadas para um pool de backend específico e uma combinação de configuração HTTP backend.

Vamos usar dois componentes seguintes:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- Usaremos o adaptador métrico para expor as métricas do Application Gateway através do servidor métrico. O Azure Kubernetes Metric Adapter é um projeto de código aberto do Azure, semelhante ao Application Gateway Ingress Controller. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- Usaremos hpa para usar métricas do Application Gateway e direcionar uma implantação para dimensionamento.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Configuração do adaptador métrico Azure Kubernetes

1. Primeiro criaremos um principal de serviço Azure `Monitoring Reader` AAD e atribuiremos o acesso ao grupo de recursos do Application Gateway. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Agora, vamos implantar [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) o uso do principal de serviço AAD criado acima.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Vamos criar `ExternalMetric` um recurso `appgw-request-count-metric`com nome. Esse recurso instruirá o adaptador métrico a expor `AvgRequestCountPerHealthyHost` métricas para `myApplicationGateway` recursos em `myResourceGroup` grupo de recursos. Você pode `filter` usar o campo para segmentar um pool de backend específico e a configuração HTTP backend no Gateway de aplicativos.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Agora você pode fazer uma solicitação ao servidor métrico para ver se nossa nova métrica está sendo exposta:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Usando a nova métrica para dimensionar a implantação

Uma vez que `appgw-request-count-metric` somos capazes de expor através [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) do servidor métrico, estamos prontos para usar para escalar nossa implantação de destino.

No exemplo a seguir, vamos `aspnet`direcionar uma implantação de amostra . Vamos escalar pods `appgw-request-count-metric` quando > 200 por Pod `10` até um máximo de Pods.

Substitua o nome de implantação do destino e aplique a seguinte configuração de escala automática:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Teste sua configuração usando uma ferramenta de teste de carga como o banco apache:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Próximas etapas
- [**Solucionar problemas do Controlador de Problemas**](ingress-controller-troubleshoot.md): Solucionar quaisquer problemas com o Controlador de Ingress.