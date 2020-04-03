---
title: Criando um controlador de entrada com um novo Gateway de Aplicativo
description: Este artigo fornece informações sobre como implantar um Controlador de Entrada de Gateway de Aplicativo com um novo Gateway de Aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b46c9f8b0cad74f3a4e9be8903270a60993c01f4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585893"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Como instalar um Controlador de Entrada de Gateway de Aplicativo (AGIC) usando um novo gateway de aplicativo

As instruções abaixo supõem que o AGIC (Application Gateway Ingress Controller, controlador de entrada de gateway) será instalado em um ambiente sem componentes pré-existentes.

## <a name="required-command-line-tools"></a>Ferramentas de linha de comando necessárias

Recomendamos o uso do [Azure Cloud Shell](https://shell.azure.com/) para todas as operações de linha de comando abaixo. Inicie seu shell a partir de shell.azure.com ou clicando no link:

[![Incorporar lançamento](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

Como alternativa, inicie o Cloud Shell no portal Azure usando o seguinte ícone:

![Inicialização do portal](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Seu [Azure Cloud Shell](https://shell.azure.com/) já tem todas as ferramentas necessárias. Se você optar por usar outro ambiente, certifique-se de que as seguintes ferramentas de linha de comando estão instaladas:

* `az`- Azure CLI: [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Ferramenta de linha de comando Kubernetes: [instruções de instalação](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Gerenciador de pacotes Kubernetes: [instruções de instalação](https://github.com/helm/helm/releases/latest)
* `jq`- processador JSON da linha de comando: [instruções de instalação](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Criar uma identidade

Siga as etapas abaixo para criar um [objeto principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)de serviço Azure Active Directory (AAD). Por favor, `password`registre `objectId` os `appId`valores e valores que serão usados nas seguintes etapas.

1. Criar o principal do serviço AD[(Leia mais sobre o RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    Os `appId` `password` valores da saída JSON serão usados nas etapas seguintes


1. Use `appId` o da saída do comando `objectId` anterior para obter o do novo princípio de serviço:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    A saída deste `objectId`comando é , que será usado no modelo Azure Resource Manager abaixo

1. Crie o arquivo de parâmetros que será usado na implantação do modelo do Azure Resource Manager mais tarde.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Para implantar um cluster habilitado `aksEnabledRBAC` para **RBAC,** defina o campo para`true`

## <a name="deploy-components"></a>Implantar componentes
Esta etapa adicionará os seguintes componentes à sua assinatura:

- [Serviço de Kubernetes do Azure](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) com 2 [sub-redes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), que será usada pela [AAD Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Baixe o modelo do Azure Resource Manager e modifique o modelo conforme necessário.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Implante o modelo do Azure Resource Manager usando `az cli`. Isso pode levar até 5 minutos.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Uma vez concluída a implantação, baixe `deployment-outputs.json`a saída de implantação em um arquivo chamado .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Configurar o controlador de entrada do gateway de aplicativo

Com as instruções na seção anterior, criamos e configuramos um novo cluster AKS e um Gateway de aplicativo. Agora estamos prontos para implantar um aplicativo de exemplo e um controlador de ingestão em nossa nova infra-estrutura Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Configurar credenciais Kubernetes
Para as etapas seguintes, precisamos de comando [kubectl](https://kubectl.docs.kubernetes.io/) de configuração, que usaremos para conectar ao nosso novo cluster Kubernetes. [Cloud](https://shell.azure.com/) Shell `kubectl` já instalou. Usaremos `az` a CLI para obter credenciais para kubernetes.

Obtenha credenciais para o seu AKS recém-implantado[(leia mais](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)):
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Instalar a identidade do pod AAD
  O Azure Active Directory Pod Identity fornece acesso baseado em token ao [Arm (Azure Resource Manager, gerente de recursos do Azure).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

  [A AAD Pod Identity](https://github.com/Azure/aad-pod-identity) adicionará os seguintes componentes ao cluster Kubernetes:
   * [CrDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/)Kubernetes `AzureIdentity` `AzureAssignedIdentity`: ,`AzureIdentityBinding`
   * Componente do [MIC (Controlador de Identidades Gerenciadas)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * Componente de [NMI (Identidade Gerenciada do Nó)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Para instalar a identidade aad pod no seu cluster:

   - *RBAC ativado* Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC desativado* Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Instalar o Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) é um gerente de pacotes da Kubernetes. Vamos aproveitá-lo `application-gateway-kubernetes-ingress` para instalar o pacote:

1. Instale [o Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) e `application-gateway-kubernetes-ingress` execute o seguinte para adicionar o pacote de leme:

    - *RBAC ativado* Cluster AKS

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC desativado* Cluster AKS

        ```bash
        helm init
        ```

1. Adicione o repositório AGIC do Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Instalar o Gráfico do Controlador de Entrada do Helm

1. Use `deployment-outputs.json` o arquivo criado acima e crie as seguintes variáveis.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Baixe helm-config.yaml, que configurará AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Ou copie o arquivo YAML abaixo: 
    
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

1. Edite o recém-baixado helm-config.yaml `appgw` e `armAuth`preencha as seções e .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Valores:
     - `verbosityLevel`: Define o nível de verbosidade da infra-estrutura de registro AGIC. Consulte [Níveis de registro em log](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) para obter os valores possíveis.
     - `appgw.subscriptionId`: O ID de assinatura do Azure no qual reside o Gateway do Aplicativo. Exemplo: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Nome do Grupo de Recursos Do Azure no qual o Application Gateway foi criado. Exemplo: `app-gw-resource-group`
     - `appgw.name`: Nome do Gateway do aplicativo. Exemplo: `applicationgatewayd0f0`
     - `appgw.shared`: Esta bandeira booleana `false`deve ser padrão para . Definido `true` como se você precisar de um [gateway de aplicativo compartilhado](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: Especifique o espaço de nome, que a AGIC deve observar. Isso pode ser um único valor de seqüência de strings, ou uma lista separada por comma de namespaces.
    - `armAuth.type`: poderia `aadPodIdentity` ser ou`servicePrincipal`
    - `armAuth.identityResourceID`: ID de recursos da identidade gerenciada do Azure
    - `armAuth.identityClientId`: O Rg do Cliente da Identidade. Veja abaixo para obter mais informações sobre Identidade
    - `armAuth.secretJSON`: Somente necessário quando o tipo `armAuth.type` de princípio `servicePrincipal`do serviço secreto for escolhido (quando foi definido para ) 


   > [!NOTE]
   > Os `identityResourceID` `identityClientID` valores e são criados durante as [etapas Implantar componentes](ingress-controller-install-new.md#deploy-components) e podem ser obtidos novamente usando o seguinte comando:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`no comando acima está o grupo de recursos do seu Gateway de aplicativo. `<identity-name>`é o nome da identidade criada. Todas as identidades de uma determinada assinatura podem ser listadas usando:`az identity list`


1. Instale o pacote do controlador de entrada do Gateway de Aplicativo:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Instale um aplicativo de amostragem
Agora que temos o Application Gateway, AKS e AGIC instalados, podemos instalar um aplicativo de exemplo via [Azure Cloud Shell](https://shell.azure.com/):

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
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
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Alternativamente, você pode:

* Baixe o arquivo YAML acima:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Aplique o arquivo YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Outros exemplos
Este [guia de como fazer](ingress-controller-expose-service-over-http-https.md) contém mais exemplos sobre como expor um serviço AKS via HTTP ou HTTPS, para a Internet com o Application Gateway.
