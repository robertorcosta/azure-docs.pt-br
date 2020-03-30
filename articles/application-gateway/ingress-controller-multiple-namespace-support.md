---
title: Habilitar vários suportes de namespace para o controlador de entrada de gateway de aplicativo
description: Este artigo fornece informações sobre como ativar o suporte a vários namespace sustais em um cluster Kubernetes com um Controlador de Entrada de Gateway de Aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279917"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Habilite o suporte a vários namespace em um cluster AKS com o Controlador de Entrada do Gateway de Aplicativo

## <a name="motivation"></a>Motivação
Kubernetes [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) tornam possível que um cluster Kubernetes seja particionado e alocado para subgrupos de uma equipe maior. Essas subequipes podem então implantar e gerenciar infra-estrutura com controles mais finos de recursos, segurança, configuração etc. Kubernetes permite que um ou mais recursos de entrada sejam definidos independentemente dentro de cada namespace.

A partir da versão 0.7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) pode ingerir eventos e observar vários namespaces. Se o administrador da AKS decidir usar o [App Gateway](https://azure.microsoft.com/services/application-gateway/) como entrada, todos os namespaces usarão a mesma instância do Application Gateway. Uma única instalação do Ingress Controller monitorará os namespaces acessíveis e configurará o Gateway de aplicativo com o quais está associado.

A versão 0.7 da AGIC continuará `default` a observar exclusivamente o namespace, a menos que este seja explicitamente alterado para um ou mais espaços de nome diferentes na configuração Helm (ver seção abaixo).

## <a name="enable-multiple-namespace-support"></a>Habilitar o suporte a vários namespaces
Para habilitar o suporte a vários namespaces:
1. modificar o arquivo [helm-config.yaml](#sample-helm-config-file) de uma das seguintes maneiras:
   - excluir `watchNamespace` a chave inteiramente de [helm-config.yaml](#sample-helm-config-file) - AGIC observará todos os namespaces
   - definido `watchNamespace` como uma seqüência de string vazia - AGIC observará todos os namespaces
   - adicionar vários namespaces separados por`watchNamespace: default,secondNamespace`uma comma ( ) - AAL observará esses namespaces exclusivamente
2. aplicar alterações no modelo do Leme com:`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Uma vez implantado com a capacidade de observar vários namespaces, o AGIC irá:
  - listar recursos de entrada de todos os espaços de nomes acessíveis
  - filtro para recursos de entrada anotados com`kubernetes.io/ingress.class: azure/application-gateway`
  - compõem [config](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) gateway de aplicativo combinado
  - aplicar a configuração ao Gateway de aplicativo associado via [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Configurações conflitantes
Vários recursos [de entrada de](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) namespaced poderiam instruir o AGIC a criar configurações conflitantes para um único Gateway de aplicativo. (Duas ingresses reivindicando o mesmo domínio, por exemplo.)

No topo da hierarquia - **os ouvintes** (endereço IP, porta e host) e **regras de roteamento (configurações** de ouvinte vinculante, pool de backend e HTTP) podem ser criados e compartilhados por vários namespaces/ingresses.

Por outro lado , caminhos, pools de back-end, configurações HTTP e certificados TLS podem ser criados por apenas um namespace e duplicações serão removidas.

Por exemplo, considere os seguintes recursos de `staging` `production` entrada `www.contoso.com`duplicados definidos para espaços de nome definidos e para:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Apesar dos dois recursos de `www.contoso.com` entrada exigindo tráfego para serem encaminhados para os respectivos namespaces kubernetes, apenas um backend pode atender o tráfego. A AGIC criaria uma configuração em base "por primeira vem, por primeira vez" para um dos recursos. Se dois recursos ingressais forem criados ao mesmo tempo, o anterior no alfabeto terá precedência. A partir do exemplo acima, só poderemos `production` criar configurações para a ingestão. O Gateway de aplicativo será configurado com os seguintes recursos:

  - Ouvinte:`fl-www.contoso.com-80`
  - Regra de roteamento:`rr-www.contoso.com-80`
  - Piscina de backend:`pool-production-contoso-web-service-80-bp-80`
  - Configurações HTTP:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - Sonda de saúde:`pb-production-contoso-web-service-80-websocket-ingress`

Observe que, exceto para *o ouvinte* e regra *de roteamento,*`production`os recursos do Application Gateway criados incluem o nome do namespace ( ) para o qual foram criados.

Se os dois recursos de entrada forem introduzidos no cluster AKS em diferentes pontos no tempo, é provável que a AGIC acabe em um cenário onde reconfigure o Application Gateway e remarine o tráfego de `namespace-B` para `namespace-A`.

Por exemplo, `staging` se você adicionou primeiro, o AGIC configurará o Gateway de aplicativo para direcionar o tráfego para o pool de backend de estágio. Em um estágio posterior, a introdução `production` da entrada, fará com que a AGIC `production` reprograme o Application Gateway, que iniciará o roteamento do tráfego para o pool de backend.

## <a name="restrict-access-to-namespaces"></a>Restringir o acesso a namespaces
Por padrão, a AGIC configurará o Application Gateway com base na Ingress anotada em qualquer namespace. Se você quiser limitar esse comportamento, você tem as seguintes opções:
  - limitar os namespaces, definindo explicitamente os namespaces `watchNamespace` que a AGIC deve observar através da tecla YAML em [helm-config.yaml](#sample-helm-config-file)
  - use [Role/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) para limitar a AGIC a espaços de nomes específicos

## <a name="sample-helm-config-file"></a>Amostra de arquivo de config helm
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

