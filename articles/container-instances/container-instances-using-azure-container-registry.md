---
title: Implantar imagem de contêiner do Registro de Contêineres do Azure
description: Saiba como implantar contêineres em Instâncias de Contêineres Do Azure, retirando imagens de contêineres de um registro de contêineres do Azure.
services: container-instances
ms.topic: article
ms.date: 02/18/2020
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 212624b857d65297830995018603c2627f83369b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453516"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Implantar nas Instâncias de Contêiner do Azure por meio do Registro de Contêiner do Azure

O [Registro de Contêiner do Azure](../container-registry/container-registry-intro.md) é um serviço de registro de contêiner gerenciado, baseado no Azure, usado para armazenar imagens de contêiner de Docker particulares. Este artigo descreve como extrair imagens de contêiner armazenadas em um registro de contêiner do Azure ao ser implantada no Azure Container Instances. Uma maneira recomendada de configurar o acesso ao registro é criar um principal e senha do serviço de diretório ativo do Azure e armazenar as credenciais de login em um cofre de chaves do Azure.

## <a name="prerequisites"></a>Pré-requisitos

**Registro de contêineres do Azure**: Você precisa de um registro de contêiner Do Zure — e pelo menos uma imagem de contêiner no registro — para concluir as etapas deste artigo. Se você precisar de um registro, consulte [Criar um registro de contêiner usando a CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md).

**CLI do Azure**: os exemplos de linha de comando neste artigo usam a [CLI do Azure](/cli/azure/) e são formatados para shell de Bash. Você pode [instalar a CLI do Azure](/cli/azure/install-azure-cli) localmente ou usar o [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Configurar a autenticação do registro

Em um cenário de produção onde você fornece acesso a serviços e aplicativos "sem cabeça", recomenda-se configurar o acesso ao registro usando um [principal de serviço](../container-registry/container-registry-auth-service-principal.md). Um diretor de serviço permite que você forneça [controle de acesso baseado em função](../container-registry/container-registry-roles.md) às imagens do contêiner. Por exemplo, é possível configurar uma entidade de serviço com acesso somente pull para um registro.

O Registro de Contêineres do Azure oferece opções adicionais [de autenticação](../container-registry/container-registry-authentication.md).

> [!NOTE]
> Você não pode autenticar o Registro de Contêineres do Azure para retirar imagens durante a implantação do grupo de contêineres usando uma [identidade gerenciada](container-instances-managed-identity.md) configurada no mesmo grupo de contêineres.

Na seção a seguir, você cria um cofre de chaves do Azure e uma entidade de serviço e armazena as credenciais da entidade de serviço no cofre. 

### <a name="create-key-vault"></a>Criar cofre chaves

Se você ainda não tiver um cofre no [Azure Key Vault](../key-vault/general/overview.md), crie um com a CLI do Azure usando os comandos a seguir.

Atualize a variável `RES_GROUP` com o nome do grupo de recursos existente no qual criar o cofre de chaves e `ACR_NAME` com o nome do registro de contêiner. Para a brevidade, os comandos deste artigo assumem que suas instâncias de registro, cofre de chaves e contêiner são criadas no mesmo grupo de recursos.

 Especifique um nome para o novo cofre de chaves em `AKV_NAME`. O nome do cofre deve ser exclusivo no Azure e deve ter de 3 a 24 caracteres alfanuméricos, começar com uma letra, terminar com uma letra ou um dígito e não pode conter hífens consecutivos.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Criar entidades de serviço e armazenar credenciais

Agora crie um diretor de serviço e armazene suas credenciais no cofre principal.

O comando a seguir usa [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar a entidade de serviço e [az keyvault secret set][az-keyvault-secret-set] para armazenar a **senha** da entidade de serviço no cofre.

```azurecli
# Create service principal, store its password in vault (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

O argumento `--role` no comando anterior configura a entidade de serviço com a função *acrpull*, que permite a ela acesso somente de pull ao Registro. Para permitir acesso de push e pull, altere o argumento `--role` para *acrpush*.

Em seguida, armazene o *appId* da entidade de serviço no cofre, que é o **nome de usuário** que você passa para o Registro de Contêiner do Azure para autenticação.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Você criou um cofre de chaves do Azure e armazenou dois segredos nele:

* `$ACR_NAME-pull-usr`: a ID da entidade de serviço, para uso como o **nome de usuário** do registro de contêiner.
* `$ACR_NAME-pull-pwd`: a senha da entidade de serviço, para uso como a **senha** do registro de contêiner.

Agora, é possível referenciar esses segredos pelo nome quando você ou os aplicativos e serviços efetuam pull de imagens do registro.

## <a name="deploy-container-with-azure-cli"></a>Implantar contêiner com CLI do Azure

Agora que as credenciais da entidade de serviço estão armazenadas nos segredos do Azure Key Vault, os aplicativos e serviços podem utilizá-las para acessar o registro privado.

Primeiro, obtenha o nome do servidor de logon do registro usando o comando [az acr show][az-acr-show]. O nome do servidor de logon é composto por letras minúsculas e é semelhante a `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Execute o seguinte comando [az container create][az-container-create] para implantar uma instância de contêiner. O comando usa as credenciais da entidade de serviço armazenadas no Azure Key Vault para autenticar no registro de contêiner e assume que você efetuou push da imagem [aci-helloworld](container-instances-quickstart.md) anteriormente para o registro. Atualize o valor `--image` se você quiser usar uma imagem diferente do registro.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

O valor `--dns-name-label` deve ser exclusivo no Azure, portanto, o comando anterior acrescenta um número aleatório ao rótulo do nome DNS do contêiner. A saída do comando exibe o FQDN (nome de domínio totalmente qualificado) do contêiner, por exemplo:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Depois que o contêiner for iniciado com êxito, será possível navegar para o FQDN no navegador para verificar se o aplicativo está sendo executado com êxito.

## <a name="deploy-with-azure-resource-manager-template"></a>Implantar com o modelo do Azure Resource Manager

Você pode especificar as propriedades do registro de contêiner do Azure em um modelo do Azure Resource Manager, incluindo a `imageRegistryCredentials` propriedade na definição de grupo de contêiner. Por exemplo, você pode especificar as credenciais de registro diretamente:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Para configurações completas do grupo de contêineres, consulte a [referência de modelo do Gerenciador de recursos](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups).    

Para obter detalhes sobre como referenciar segredos do Azure Key Vault em um modelo do Resource Manager, consulte [Usar o Azure Key Vault para passar o valor de parâmetro seguro durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Implantar com o portal do Azure

Se você mantiver as imagens de contêiner no Registro de Contêiner do Azure, você poderá facilmente criar um contêiner em Instâncias de Contêiner do Azure usando o portal do Azure. Ao usar o portal para implantar uma instância de contêiner de um registro de contêiner, você deve habilitar a [conta de administrador](../container-registry/container-registry-authentication.md#admin-account) do registro. A conta do administrador destina-se para um único usuário acessar o registro, principalmente para fins de teste. 

1. No Portal do Azure, navegue até o registro de contêiner.

1. Para confirmar que a conta de administrador é habilitada, selecione **Chaves de acesso**e, em **Usuário administrador** selecione **Habilitar**.

1. Selecione **Repositórios** e, em seguida, selecione o repositório do qual deseja implantar, clique com o botão direito do mouse na marca da imagem de contêiner que deseja implantar e selecione **Executar instância**.

    !["Executar instância" no Registro de Contêiner do Azure no portal do Azure][acr-runinstance-contextmenu]

1. Insira um nome para o contêiner e um nome para o grupo de recursos. Você também poderá alterar os valores padrão se desejar.

    ![Criar menu para Instâncias de Contêiner do Azure][acr-create-deeplink]

1. Quando a implantação for concluída, você poderá navegar para o grupo de contêineres do painel de notificações para localizar o endereço IP e outras propriedades do contêiner.

    ![Exibição de detalhes de grupo de contêineres das Instâncias de Contêiner do Azure][aci-detailsview]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a autenticação do Registro de Contêiner do Azure, consulte [Autenticar com um registro de contêiner do Azure](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
