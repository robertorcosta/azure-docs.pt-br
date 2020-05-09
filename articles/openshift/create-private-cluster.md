---
title: Criar um cluster privado do Azure Red Hat OpenShift 4
description: Saiba como criar um cluster particular do Azure Red Hat OpenShift executando o OpenShift 4
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: toa, openshift, AZ aro, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: cfc28577f089ef22457e9f66ff08106969a5a4b2
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857381"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Criar um cluster privado do Azure Red Hat OpenShift 4

Neste artigo, você irá preparar seu ambiente para criar clusters privados do Azure Red Hat OpenShift executando o OpenShift 4. Você aprenderá a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e as sub-redes necessárias
> * Implantar um cluster com um ponto de extremidade do servidor de API privada e um controlador de entrada privado

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando o CLI do Azure versão 2.0.75 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Antes de começar

### <a name="install-the-az-aro-extension"></a>Instalar a extensão ' AZ toa '
A `az aro` extensão permite que você crie, acesse e exclua clusters do Azure Red Hat OpenShift diretamente da linha de comando usando o CLI do Azure.

Execute o comando a seguir para instalar `az aro` a extensão.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Se você já tiver a extensão instalada, poderá atualizar executando o comando a seguir.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Registre o provedor de recursos

Em seguida, você precisa registrar o `Microsoft.RedHatOpenShift` provedor de recursos em sua assinatura.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Verifique se a extensão está registrada.

```azurecli-interactive
az -v
```

  Você deve obter uma saída semelhante à mostrada abaixo.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Obter um segredo de pull do Red Hat (opcional)

Um segredo de pull do Red Hat permite que o cluster acesse registros de contêiner do Red Hat juntamente com conteúdo adicional. Esta etapa é opcional, mas recomendada.

1. **[Acesse o portal do Gerenciador de cluster do Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) e faça logon.**

   Você precisará fazer logon em sua conta do Red Hat ou criar uma nova conta do Red Hat com seu email comercial e aceitar os termos e condições.

2. **Clique em baixar segredo de pull.**

Mantenha o arquivo `pull-secret.txt` salvo em algum lugar seguro-ele será usado em cada criação de cluster.

Ao executar o `az aro create` comando, você pode fazer referência ao seu segredo de `--pull-secret @pull-secret.txt` pull usando o parâmetro. Execute `az aro create` a partir do diretório em que você `pull-secret.txt` armazenou o arquivo. Caso contrário, `@pull-secret.txt` substitua `@<path-to-my-pull-secret-file`por.

Se você estiver copiando seu segredo de pull ou fazendo referência a ele em outros scripts, o segredo de pull deverá ser formatado como uma cadeia de caracteres JSON válida.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Criar uma rede virtual contendo duas sub-redes vazias

Em seguida, você criará uma rede virtual contendo duas sub-redes vazias.

1. **Defina as variáveis a seguir.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Criar um grupo de recursos**

    Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Ao criar um grupo de recursos, você é solicitado a especificar um local. Essa é a localização na qual os metadados do grupo de recursos são armazenados e na qual os recursos são executados no Azure, caso você não especifique outra região durante a criação de recursos. Crie um grupo de recursos usando o comando [AZ Group Create] [AZ-Group-Create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    A seguinte saída de exemplo mostra o grupo de recursos criado com êxito:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Criar uma rede virtual.**

    Os clusters do Azure Red Hat OpenShift que executam o OpenShift 4 exigem uma rede virtual com duas sub-redes vazias, para os nós mestre e de trabalho.

    Crie uma nova rede virtual no mesmo grupo de recursos que você criou anteriormente.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    A saída de exemplo a seguir mostra a rede virtual criada com êxito:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Adicione uma sub-rede vazia para os nós mestres.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Adicione uma sub-rede vazia para os nós de trabalho.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Desabilite as políticas de ponto de extremidade privado da sub-rede](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) na sub-rede mestre.** Isso é necessário para poder conectar e gerenciar o cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Criar o cluster

Execute o comando a seguir para criar um cluster. Opcionalmente, você pode [passar seu segredo de pull do Red Hat](#get-a-red-hat-pull-secret-optional) , que permite que o cluster acesse registros de contêiner do Red Hat juntamente com conteúdo adicional.

>[!NOTE]
> Se você estiver copiando/colando comandos e usando um dos parâmetros opcionais, certifique-se de excluir as hashtags iniciais e o texto do comentário à direita. Além de isso, feche o argumento na linha anterior do comando com uma barra invertida à direita.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Depois de executar `az aro create` o comando, normalmente leva cerca de 35 minutos para criar um cluster.

>[!IMPORTANT]
> Se você optar por especificar um domínio personalizado, por exemplo, **foo.example.com**, o console do OpenShift estará disponível em uma URL como `https://console-openshift-console.apps.foo.example.com`, em vez do domínio `https://console-openshift-console.apps.<random>.<location>.aroapp.io`interno.
>
> Por padrão, o OpenShift usa certificados autoassinados para todas as rotas criadas `*.apps.<random>.<location>.aroapp.io`em.  Se você escolher DNS personalizado, depois de se conectar ao cluster, será necessário seguir a documentação do OpenShift para [Configurar uma autoridade de certificação personalizada para seu controlador de entrada](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) e [AC personalizada para seu servidor de API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

## <a name="connect-to-the-private-cluster"></a>Conectar-se ao cluster privado

Você pode fazer logon no cluster usando o `kubeadmin` usuário.  Execute o comando a seguir para localizar a senha do `kubeadmin` usuário.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

A saída de exemplo a seguir mostra que a senha `kubeadminPassword`estará em.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Você pode encontrar a URL do console de cluster executando o comando a seguir, que será semelhante a`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Para se conectar a um cluster privado do Azure Red Hat OpenShift, você precisará executar a etapa a seguir de um host que esteja na rede virtual que você criou ou em uma rede virtual [emparelhada](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) com a rede virtual em que o cluster foi implantado.

Inicie a URL do console em um navegador e faça logon `kubeadmin` usando as credenciais.

![Tela de logon do Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Instalar a CLI do OpenShift

Quando você estiver conectado ao console Web do OpenShift, clique em **?** no canto superior direito e em **ferramentas de linha de comando**. Baixe a versão apropriada para seu computador.

![Tela de logon do Azure Red Hat OpenShift](media/aro4-download-cli.png)

Você também pode baixar a versão mais recente da CLI apropriada para o seu computador <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>do.

## <a name="connect-using-the-openshift-cli"></a>Conectar-se usando a CLI do OpenShift

Recupere o endereço do servidor de API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Para se conectar a um cluster privado do Azure Red Hat OpenShift, você precisará executar a etapa a seguir de um host que esteja na rede virtual que você criou ou em uma rede virtual [emparelhada](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) com a rede virtual em que o cluster foi implantado.

Faça logon no servidor de API do cluster OpenShift usando o comando a seguir. Substitua ** \<a senha kubeadmin>** pela senha que você acabou de recuperar.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, um cluster do Azure Red Hat OpenShift que executa o OpenShift 4 foi implantado. Você aprendeu a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e as sub-redes necessárias
> * Implantar um cluster
> * Conectar-se ao cluster usando `kubeadmin` o usuário

Avance para o próximo artigo para saber como configurar o cluster para autenticação usando Azure Active Directory.


* [Configurar a autenticação com Azure Active Directory usando a linha de comando](configure-azure-ad-cli.md)


* [Configurar a autenticação com Azure Active Directory usando o console Web portal do Azure e OpenShift](configure-azure-ad-cli.md)
