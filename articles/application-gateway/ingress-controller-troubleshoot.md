---
title: Solução de problemas do controlador de entrada de gateway de aplicativo
description: Este artigo fornece documentação sobre como solucionar problemas e/ou problemas comuns com o Controlador de Entrada de Gateway de Aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795507"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Solucionando problemas ou problemas comuns com o Controlador de Ingress

[O Azure Cloud Shell](https://shell.azure.com/) é a maneira mais conveniente de solucionar quaisquer problemas com sua instalação AKS e AGIC. Inicie seu shell a partir de [shell.azure.com](https://shell.azure.com/) ou clicando no link:

[![Incorporar lançamento](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Teste com um simples aplicativo Kubernetes

As etapas abaixo supõem:
  - Você tem um cluster AKS, com a advanced networking ativada
  - AGIC foi instalado no cluster AKS
  - Você já possui um Gateway de aplicativo em um VNET compartilhado com seu cluster AKS

Para verificar se a instalação do Application Gateway + AKS + AGIC está configurada corretamente, implante o aplicativo mais simples possível:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Copie e cole todas as linhas de uma só vez do script acima em uma [Shell nuvem azure](https://shell.azure.com/). Certifique-se de que todo o `cat` comando é `EOF`copiado - começando com e incluindo o último .

![aplicar](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Após uma implantação bem-sucedida do aplicativo acima do seu cluster AKS terá um novo Pod, Serviço e uma Ingress.

Obtenha a lista de pods `kubectl get pods -o wide`com Cloud [Shell](https://shell.azure.com/): .
Esperamos que um pod chamado 'test-agic-app-pod' tenha sido criado. Ele terá um endereço IP. Este endereço deve estar dentro do VNET do Gateway de aplicativo, que é usado com AKS.

![Vagens](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Veja a lista `kubectl get services -o wide`de serviços: . Esperamos ver um serviço chamado 'test-agic-app-service'.

![Vagens](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Veja a lista das ingressas: `kubectl get ingress`. Esperamos que um recurso ingress chamado 'test-agic-app-ingress' tenha sido criado. O recurso terá um nome de host 'test.agic.contoso.com'.

![Vagens](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Uma das cápsulas será AGIC. `kubectl get pods`mostrará uma lista de pods, um dos quais começará com "ingress-azure". Obtenha todos os registros `kubectl logs <name-of-ingress-controller-pod>` desse pod para verificar se tivemos uma implantação bem sucedida. Uma implantação bem-sucedida teria adicionado as seguintes linhas ao log:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Alternativamente, a partir do [Cloud Shell](https://shell.azure.com/) podemos recuperar apenas `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`as `<ingress-azure....>` linhas indicando configuração bem-sucedida do Application Gateway com , onde deve ser o nome exato do pod AGIC.

O Application Gateway terá a seguinte configuração aplicada:

- Ouvinte: ![ouvinte](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Regra de ![roteamento: routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Piscina de backend:
  - Haverá um endereço IP no pool de endereços backend e ele corresponderá `kubectl get pods -o wide` 
 ![ao endereço IP do Pod que observamos anteriormente com backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Finalmente, podemos `cURL` usar o comando de dentro do [Cloud Shell](https://shell.azure.com/) para estabelecer uma conexão HTTP com o aplicativo recém-implantado:

1. Use `kubectl get ingress` para obter o endereço IP público do Gateway de aplicativo
2. Use `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`.

![Vagens](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Um resultado `HTTP/1.1 200 OK` indica que o sistema Application Gateway + AKS + AGIC está funcionando como esperado.


## <a name="inspect-kubernetes-installation"></a>Inspecione a instalação do Kubernetes

### <a name="pods-services-ingress"></a>Pods, Serviços, Ingress
O AGIC (Application Gateway Ingress Controller, controlador de entrada de gateway de aplicativos) monitora continuamente os seguintes recursos do Kubernetes: [Implantação](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) ou [Pod,](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod) [Serviço,](https://kubernetes.io/docs/concepts/services-networking/service/) [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


O seguinte deve estar em vigor para que a AGIC funcione como esperado:
  1. AKS deve ter uma ou mais **cápsulas saudáveis.**
     Verifique isso no [Cloud Shell](https://shell.azure.com/) com `kubectl get pods -o wide --show-labels` Se `apsnetapp`você tiver um Pod com um , sua saída pode ser assim:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Um ou mais **serviços**, fazendo `selector` referência aos pods acima através de etiquetas correspondentes.
     Verifique isso na [Cloud Shell](https://shell.azure.com/) com`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress**, anotado `kubernetes.io/ingress.class: azure/application-gateway`com , referenciando o serviço acima Verifique isso da [Cloud Shell](https://shell.azure.com/) com`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Veja as anotações da ingestão `kubectl get ingress aspnetapp -o yaml` acima: `aspnetapp` (substitua com o nome da sua ingressa)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     O recurso de ingestão deve ser `kubernetes.io/ingress.class: azure/application-gateway`anotado com .
 

### <a name="verify-observed-namespace"></a>Verifique o espaço de nome observado

* Obtenha os namespaces existentes no cluster Kubernetes. Em que namespace seu aplicativo está sendo executado? A AGIC está assistindo esse namespace? Consulte a documentação de suporte a [vários namespaces](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) sobre como configurar corretamente os namespaces observados.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* O pod AGIC deve `default` estar no `NAMESPACE`namespace (ver coluna ). Uma cápsula saudável `Running` teria `STATUS` na coluna. Deve haver pelo menos uma cápsula AGIC.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Se a cápsula AGIC`STATUS` não estiver saudável (coluna do comando acima não `Running`é ):
  - obter logs para entender por que:`kubectl logs <pod-name>`
  - para a instância anterior do pod:`kubectl logs <pod-name> --previous`
  - descrever o pod para obter mais contexto:`kubectl describe pod <pod-name>`


* Você tem recursos do [Serviço](https://kubernetes.io/docs/concepts/services-networking/service/) Kubernetes e [ingress?](https://kubernetes.io/docs/concepts/services-networking/ingress/)
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Sua [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) está anotada `kubernetes.io/ingress.class: azure/application-gateway`com: ? A AGIC só vai observar os recursos do Kubernetes Ingress que tenham essa anotação.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* A AGIC emite eventos Kubernetes para certos erros críticos. Você pode ver estes:
  - em seu terminal via`kubectl get events --sort-by=.metadata.creationTimestamp`
  - no seu navegador usando a [Interface do Usuário da Kubernetes (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Níveis de log

AGIC tem 3 níveis de registro. O nível 1 é o padrão e mostra um número mínimo de linhas de log.
O nível 5, por outro lado, exibiria todos os registros, incluindo o conteúdo higienizado da configuração aplicada ao ARM.

A comunidade Kubernetes estabeleceu 9 níveis de registro para a ferramenta [kubectl.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) Neste repositório estamos utilizando 3 destes, com semântica semelhante:


| Detalhamento | Descrição |
|-----------|-------------|
|  1        | Nível de registro padrão; mostra detalhes da inicialização, avisos e erros |
|  3        | Informações estendidas sobre eventos e alterações; listas de objetos criados |
|  5        | Logs empacotados objetos; mostra configuração JSON higienizada aplicada ao ARM |


Os níveis de verbosidade `verbosityLevel` são ajustáveis através da variável no arquivo [helm-config.yaml.](#sample-helm-config-file) Aumente o nível de `5` verbosidade para obter a configuração JSON enviada para [arm](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview):
  - adicionar `verbosityLevel: 5` em uma linha por si só em [helm-config.yaml](#sample-helm-config-file) e re-instalar
  - obter logs com`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Amostra de arquivo de config helm
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

