---
title: Usar um endereço IP estático com o balanceador de carga do Serviço do Kubernetes do Azure (AKS)
description: Saiba como criar e usar um endereço IP estático com o balanceador de carga do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/06/2019
ms.author: mlearned
ms.openlocfilehash: 8457f1c0c5b6107c4b44f6f00236a33f7c67452a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325438"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usar um endereço IP público estático com o balanceador de carga do AKS (Serviço de Kubernetes do Azure)

Por padrão, o endereço IP público atribuído a um recurso de balanceador de carga criado por um cluster do AKS só é válido durante o tempo de vida do recurso. Se você excluir o serviço de Kubernetes, o balanceador de carga e o endereço IP associados também serão excluídos. Se você quiser atribuir um endereço IP específico ou manter um endereço IP para serviços de Kubernetes reimplantados, crie e use um endereço IP público estático.

Este artigo mostra como criar um endereço IP público estático e atribuí-lo ao serviço de Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. If you need an AKS cluster, see the AKS quickstart [using the Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

You also need the Azure CLI version 2.0.59 or later installed and configured. Execute  `az --version` para encontrar a versão. If you need to install or upgrade, see [Install Azure CLI][install-azure-cli].

This article covers using a *Standard* SKU IP with a *Standard* SKU load balancer. For more information, see [IP address types and allocation methods in Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Create a static public IP address with the [az network public ip create][az-network-public-ip-create] command. The following creates a static IP resource named *myAKSPublicIP* in the *myResourceGroup* resource group:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> If you are using a *Basic* SKU load balancer in your AKS cluster, use *Basic* for the *sku* parameter when defining a public IP. Only *Basic* SKU IPs work with the *Basic* SKU load balancer and only *Standard* SKU IPs work with *Standard* SKU load balancers. 

The IP address is displayed, as shown in the following condensed example output:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

You can later get the public IP address using the [az network public-ip list][az-network-public-ip-list] command. Especifique o nome do grupo de recursos do nó e o endereço IP público que você criou e, em seguida, confira o *ipAddress*, conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço usando o endereço IP estático

Before creating a service, ensure the service principal used by the AKS cluster has delegated permissions to the other resource group. Por exemplo:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

To create a *LoadBalancer* service with the static public IP address, add the `loadBalancerIP` property and the value of the static public IP address to the YAML manifest. Crie um arquivo chamado `load-balancer-service.yaml` e copie no YAML a seguir. Forneça seu próprio endereço IP público criado na etapa anterior. The following example also sets the annotation to the resource group named *myResourceGroup*. Provide your own resource group name.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Crie o serviço e a implantação com o comando `kubectl apply`.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="troubleshoot"></a>Solucionar problemas

If the static IP address defined in the *loadBalancerIP* property of the Kubernetes service manifest does not exist, or has not been created in the node resource group and no additional delegations configured, the load balancer service creation fails. To troubleshoot, review the service creation events with the [kubectl describe][kubectl-describe] command. Forneça o nome do serviço, conforme especificado no manifesto do YAML, como é mostrado no exemplo a seguir:

```console
kubectl describe service azure-load-balancer
```

As informações sobre o recurso do serviço de Kubernetes são exibidas. Os *Eventos* no final da saída de exemplo a seguir indicam que o *endereço IP fornecido pelo usuário não foi encontrado*. Nesses cenários, verifique se você criou o endereço IP público estático no grupo de recursos do nó e se o endereço IP especificado no manifesto do serviço de Kubernetes está correto.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Próximos passos

For additional control over the network traffic to your applications, you may want to instead [create an ingress controller][aks-ingress-basic]. You can also [create an ingress controller with a static public IP address][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
