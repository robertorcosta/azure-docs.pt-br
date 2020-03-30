---
title: Crie um controlador de entrada com um Gateway de aplicativo existente
description: Este artigo fornece informações sobre como implantar um Controlador de Entrada de Gateway de Aplicativo com um Gateway de Aplicativo existente.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 048ab7249b27839890bab3e677154ca3c7a0cc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239429"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Instale um AGIC (Application Gateway Ingress Controller, controlador de entrada de gateway de aplicativos) usando um gateway de aplicativo existente

O AGIC (Application Gateway Ingress Controller, controlador de entrada de entrada) é um pod dentro do cluster Kubernetes.
A AGIC monitora os recursos do Kubernetes [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) e cria e aplica a configuração do Application Gateway com base no status do cluster Kubernetes.

## <a name="outline"></a>Esboço:
- [Pré-requisitos](#prerequisites)
- [Autenticação do Azure Resource Manager (ARM)](#azure-resource-manager-authentication)
    - Opção 1: [Configure aad-pod-identity](#set-up-aad-pod-identity) e crie a identidade Azure em ARMs
    - Opção 2: [Usar um diretor de serviço](#using-a-service-principal)
- [Instale o Controlador de Ingress usando o Helm](#install-ingress-controller-as-a-helm-chart)
- [Gateway de aplicativo compartilhado](#multi-cluster--shared-application-gateway)/ multicluster : Instale AGIC em um ambiente, onde o Application Gateway é compartilhado entre um ou mais clusters AKS e/ou outros componentes do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você já tenha as seguintes ferramentas e infra-estrutura instaladas:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) com [rede avançada ativada](https://docs.microsoft.com/azure/aks/configure-azure-cni)
- [Gateway de aplicativo v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) na mesma rede virtual que a AKS
- [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) instalado em seu cluster AKS
- [Cloud Shell](https://shell.azure.com/) é o ambiente shell `az` Do `kubectl`Zure, que tem CLI, e `helm` instalado. Essas ferramentas são necessárias para os comandos abaixo.

Faça __backup da configuração do Application Gateway__ antes de instalar o AGIC:
  1. usando [o portal Azure](https://portal.azure.com/) navegar para a sua `Application Gateway` instância
  2. a `Export template` partir do clique`Download`

O arquivo zip que você baixou terá modelos JSON, bash e scripts PowerShell que você pode usar para restaurar o App Gateway se isso se tornar necessário

## <a name="install-helm"></a>Instalar o Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) é um gerente de pacotes da Kubernetes. Vamos aproveitá-lo `application-gateway-kubernetes-ingress` para instalar o pacote.
Use [cloud shell](https://shell.azure.com/) para instalar o Helm:

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

## <a name="azure-resource-manager-authentication"></a>Autenticação do Azure Resource Manager

A AGIC se comunica com o servidor de API kubernetes e o Gerenciador de Recursos do Azure. Requer uma identidade para acessar essas APIs.

## <a name="set-up-aad-pod-identity"></a>Configure a identidade do pod AAD

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) é um controlador, semelhante ao AGIC, que também é executado em seu AKS. Ele liga as identidades do Diretório Ativo do Azure aos seus pods Kubernetes. A identidade é necessária para que um aplicativo em um pod Kubernetes seja capaz de se comunicar com outros componentes do Azure. No caso específico aqui, precisamos de autorização para que o pod AGIC faça solicitações HTTP à [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Siga as [instruções de instalação da AAD Pod Identity](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) para adicionar este componente ao seu AKS.

Em seguida, precisamos criar uma identidade Azure e dar-lhe permissões ARM.
Use [o Cloud Shell](https://shell.azure.com/) para executar todos os seguintes comandos e criar uma identidade:

1. Crie uma identidade Azure **no mesmo grupo de recursos que os nomes AKS**. Escolher o grupo de recursos correto é importante. O grupo de recursos exigido no comando abaixo *não* é o mencionado no painel do portal AKS. Este é o grupo `aks-agentpool` de recursos das máquinas virtuais. Normalmente, esse grupo `MC_` de recursos começa com e contém o nome do seu AKS. Por exemplo:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Para os comandos de atribuição `principalId` de função abaixo, precisamos obter para a identidade recém-criada:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Dê acesso `Contributor` à identidade ao gateway de aplicativos. Para isso, você precisa do ID do Gateway de aplicativo, que será algo parecido com isso:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Obtenha a lista de IDs do Application Gateway em sua assinatura com:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Dê acesso `Reader` à identidade ao grupo de recursos Application Gateway. O ID do grupo `/subscriptions/A/resourceGroups/B`de recursos seria como: . Você pode obter todos os grupos de recursos com:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Usando um diretor de serviço
Também é possível fornecer acesso AGIC ao ARM através de um segredo Kubernetes.

1. Crie um diretor de serviço de diretório ativo e codifique com base64. A codificação base64 é necessária para que a bolha JSON seja salva em Kubernetes.

```azurecli
az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0
```

2. Adicione a bolha JSON codificada base64 ao `helm-config.yaml` arquivo. Mais informações `helm-config.yaml` estão na próxima seção.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Instale o Controlador de Ingress como um gráfico de leme
Nos primeiros passos, instalamos o Letiller do Helm no seu cluster Kubernetes. Use [cloud shell](https://shell.azure.com/) para instalar o pacote AGIC Helm:

1. Adicione `application-gateway-kubernetes-ingress` o repo de leme e realize uma atualização de leme

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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

1. Editar helm-config.yaml e preencher `appgw` os `armAuth`valores para e .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > E `<identity-resource-id>` `<identity-client-id>` são as propriedades do Azure AD Identity que você configurou na seção anterior. Você pode recuperar essas informações executando `az identity show -g <resourcegroup> -n <identity-name>`o `<resourcegroup>` seguinte comando: , onde está o grupo de recursos no qual o objeto de cluster AKS de nível superior, o Gateway de aplicativo e o Managed Identify são implantados.

1. Instale `application-gateway-kubernetes-ingress` o `helm-config.yaml` gráfico Helm com a configuração da etapa anterior

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Alternativamente, você `helm-config.yaml` pode combinar o comando Helm em um passo:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Verifique o registro do pod recém-criado para verificar se ele começou corretamente

Consulte [este guia de como entender](ingress-controller-expose-service-over-http-https.md) como você pode expor um serviço AKS via HTTP ou HTTPS, para a internet, usando um Gateway de aplicativo Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Gateway de aplicativo compartilhado/multicluster
Por padrão, a AGIC assume a propriedade total do Gateway de aplicativo ao que está vinculado. A versão AGIC 0.8.0 e posteriorpode compartilhar um único Gateway de aplicativo com outros componentes do Azure. Por exemplo, poderíamos usar o mesmo Gateway de aplicativo para um aplicativo hospedado no Virtual Machine Scale Set, bem como um cluster AKS.

Faça __backup da configuração do Application Gateway__ antes de ativar essa configuração:
  1. usando [o portal Azure](https://portal.azure.com/) navegar para a sua `Application Gateway` instância
  2. a `Export template` partir do clique`Download`

O arquivo zip que você baixou terá modelos JSON, bash e scripts PowerShell que você pode usar para restaurar o Application Gateway

### <a name="example-scenario"></a>Cenário de Exemplo
Vamos olhar para um Gateway de aplicativo imaginário, que gerencia o tráfego para dois sites:
  - `dev.contoso.com`- hospedado em um novo AKS, usando o Application Gateway e o AGIC
  - `prod.contoso.com`- hospedado em [um conjunto de escala de máquina virtual do Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Com as configurações padrão, a AGIC assume 100% de propriedade do Gateway de aplicativo para o que é apontado. AGIC substitui toda a configuração do App Gateway. Se criarmos manualmente um ouvinte `prod.contoso.com` para (no Application Gateway), sem defini-lo no Kubernetes `prod.contoso.com` Ingress, o AGIC excluirá a configuração em segundos.

Para instalar o AGIC e também servir `prod.contoso.com` a partir de nossas `dev.contoso.com` máquinas virtual machine scale set, devemos restringir a AGIC à configuração apenas. Isso é facilitado pela instanção do [crd](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)seguinte:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

O comando acima `AzureIngressProhibitedTarget` cria um objeto. Isso torna o AGIC (versão 0.8.0 e posterior) ciente `prod.contoso.com` da existência da configuração do Application Gateway para e o instrui explicitamente para evitar alterar qualquer configuração relacionada a esse nome de host.


### <a name="enable-with-new-agic-installation"></a>Habilite com nova instalação AGIC
Para limitar o AGIC (versão 0.8.0 e posterior) a `helm-config.yaml` um subconjunto da configuração Do Gateway de Aplicativo, modifique o modelo.
Na `appgw:` seção, `shared` adicione a tecla e defina-a como `true`.

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Aplicar as alterações do Leme:
  1. Certifique-se de que o `AzureIngressProhibitedTarget` CRD está instalado com:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Atualizar helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Como resultado, seu AKS terá `AzureIngressProhibitedTarget` uma `prohibit-all-targets`nova instância chamada:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

O `prohibit-all-targets`objeto , como o nome indica, proíbe a AGIC de alterar a configuração para *qualquer* host e caminho.
A instalação `appgw.shared=true` do Helm com irá implantar o AGIC, mas não fará alterações no Gateway de aplicativo.


### <a name="broaden-permissions"></a>Ampliar permissões
Uma vez `appgw.shared=true` que `prohibit-all-targets` helm com e o padrão bloqueia AGIC de aplicar qualquer configuração.

Ampliar as permissões AGIC com:
1. Crie um `AzureIngressProhibitedTarget` novo com sua configuração específica:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Somente depois de ter criado sua própria proibição personalizada, você pode excluir a padrão, que é muito ampla:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Habilitar para uma instalação AGIC existente
Vamos supor que já temos um AKS funcionando, o Application Gateway e o AGIC configurado em nosso cluster. Temos uma Ingress `prod.contosor.com` para e estamos servindo com sucesso o tráfego para ele a partir de AKS. Queremos adicionar `staging.contoso.com` ao nosso Gateway de aplicativo existente, mas precisamos hospedá-lo em uma [VM](https://azure.microsoft.com/services/virtual-machines/). Vamos reutilizar o Gateway de aplicativo existente e configurar manualmente um pool `staging.contoso.com`de ouvintes e backend para . Mas ajustar manualmente a configuração do Gateway de aplicativo (via [portal,](https://portal.azure.com) [APIs ARM](https://docs.microsoft.com/rest/api/resources/) ou [Terraform)](https://www.terraform.io/)entraria em conflito com as suposições da AGIC de propriedade total. Logo após aplicarmos alterações, a AGIC irá sobrescrevê-las ou excluí-las.

Podemos proibir a AGIC de fazer alterações em um subconjunto de configuração.

1. Criar `AzureIngressProhibitedTarget` um objeto:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Exibir o objeto recém-criado:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Modificar configuração do Gateway de aplicativo via portal - adicionar ouvintes, regras de roteamento, backends etc. O novo objeto que`manually-configured-staging-environment`criamos ( ) proibirá a AGIC de substituir a configuração do Gateway de aplicativo relacionada a `staging.contoso.com`.
