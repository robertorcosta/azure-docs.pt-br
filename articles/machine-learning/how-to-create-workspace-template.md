---
title: Criar um workspace com um modelo do Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Saiba como usar um modelo do Azure Resource Manager para criar um workspace do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 07/09/2020
ms.custom: seoapril2019
ms.openlocfilehash: 4ba48e5beb8ce4b4ae126dd23acbe0dec650f655
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232144"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Usar um modelo do Azure Resource Manager para criar um workspace para o Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

Neste artigo, você aprenderá várias maneiras de criar um workspace do Azure Machine Learning usando modelos do Azure Resource Manager. Um modelo do Resource Manager facilita a criação de recursos como uma operação única e coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Além disso, pode especificar os parâmetros de implantação. Os parâmetros são usados para fornecer valores de entrada ao usar o modelo.

Para saber mais, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Se você não tiver uma, experimente a [versão paga ou gratuita do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar um modelo a partir de uma CLI, você precisará do [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="workspace-resource-manager-template"></a>Modelo do Resource Manager do espaço de trabalho

O modelo de Azure Resource Manager usado em todo este documento pode ser encontrado no diretório [201-Machine-Learning-Advanced](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) do repositório do GitHub de modelos de início rápido do Azure.

Esse modelo cria os seguintes serviços do Azure:

* Conta de Armazenamento do Azure
* Cofre de Chave do Azure
* Azure Application Insights
* Registro de Contêiner do Azure
* Workspace do Azure Machine Learning

O grupo de recursos é o contêiner que retém os serviços. Os diversos serviços são exigidos pelo workspace do Azure Machine Learning.

O modelo de exemplo tem dois parâmetros **obrigatórios** :

* O **local** onde os recursos serão criados.

    O modelo usará a localização selecionada para a maioria dos recursos. A exceção é o serviço do Application Insights que não está disponível em todos os locais de disponibilidade dos outros serviços. Se você selecionar uma localização onde não esteja disponível, o serviço será criado na localização Centro-Sul dos EUA.

* O **WorkspaceName**, que é o nome amigável do espaço de trabalho Azure Machine Learning.

    > [!NOTE]
    > O nome do workspace não diferencia maiúsculas de minúsculas.

    Os nomes dos outros serviços são gerados aleatoriamente.

> [!TIP]
> Embora o modelo associado a este documento crie um Registro de Contêiner do Azure, você também pode criar um workspace sem criar nenhum registro de contêiner. Um registro de contêiner será criado quando você executar uma operação que exija um. Treinar ou implantar um modelo, por exemplo.
>
> Você também pode fazer referência a uma conta de armazenamento ou registro de contêiner existente no modelo do Azure Resource Manager, em vez de criar um. No entanto, o registro de contêiner que você usa deve ter a __conta de administrador__ habilitada. Para obter informações sobre como habilitar a conta de administrador, consulte [conta de administrador](/azure/container-registry/container-registry-authentication#admin-account).

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Para obter mais informações sobre modelos, consulte os artigos a seguir:

* [Criar modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/templates/template-syntax.md)
* [Implantar um aplicativo com o modelo do Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Tipos de recursos do Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>Implantar modelo

Para implantar seu modelo, você precisa criar um grupo de recursos.

Consulte a seção [portal do Azure](#use-the-azure-portal) se preferir usar a interface gráfica do usuário.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

Depois que o grupo de recursos for criado com êxito, implante o modelo com o seguinte comando:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

Por padrão, todos os recursos criados como parte do modelo são novos. No entanto, você também tem a opção de usar os recursos existentes. Ao fornecer parâmetros adicionais ao modelo, você pode usar os recursos existentes. Por exemplo, se você quiser usar uma conta de armazenamento existente, defina o valor de **storageAccountOption** como **existente** e forneça o nome da sua conta de armazenamento no parâmetro **storageAccountName** .

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>Implantar um espaço de trabalho criptografado

O modelo de exemplo a seguir demonstra como criar um workspace com três configurações:

* Habilitar configurações de alta confidencialidade para o workspace
* Habilitar a criptografia para o workspace
* Usa um Azure Key Vault existente para recuperar chaves gerenciadas pelo cliente

Para obter mais informações, confira [Criptografia em repouso](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]
> Há alguns requisitos específicos que sua assinatura precisa atender antes de usar este modelo:
>
> * O aplicativo __Azure Machine Learning__ precisa ser um __colaborador__ para sua assinatura do Azure.
> * Você precisa ter um Azure Key Vault existente que contenha uma chave de criptografia.
> * Você precisa ter uma política de acesso no Azure Key Vault que conceda acesso a __get__, __wrap__ e __unwrap__ ao aplicativo __Azure Cosmos DB__.
> * O Azure Key Vault precisa estar na mesma região em que você planeja criar o workspace do Azure Machine Learning.

__Para adicionar o aplicativo Azure Machine Learning como um colaborador__, use os seguintes comandos:

1. Faça logon em sua conta do Azure e obtenha sua ID de assinatura. Essa assinatura precisa ser a mesma que contém o seu workspace do Azure Machine Learning.  

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az account list --query '[].[name,id]' --output tsv
    ```

    > [!TIP]
    > Para selecionar outra assinatura, use o comando `az account set -s <subscription name or ID>` e especifique o nome ou a ID da assinatura para a qual deseja alternar. Para obter mais informações sobre a seleção da assinatura, confira [Usar várias assinaturas do Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). 

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

    ```azurepowershell
    Get-AzSubscription
    ```

    > [!TIP]
    > Para selecionar outra assinatura, use o comando `Az-SetContext -SubscriptionId <subscription ID>` e especifique o nome ou a ID da assinatura para a qual deseja alternar. Para obter mais informações sobre a seleção da assinatura, confira [Usar várias assinaturas do Azure](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps?view=azps-4.3.0).

    ---

1. Para obter a ID do objeto do aplicativo do Azure Machine Learning, use o comando a seguir. O valor pode ser diferente para cada uma das suas assinaturas do Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

    ```azurepowershell
    Get-AzADServicePrincipal --DisplayName "Azure Machine Learning" | select-object DisplayName, Id
    ```

    ---
    Esse comando retorna a ID de objeto, que é um GUID.

1. Para adicionar a ID de objeto como um colaborador à sua assinatura, use o comando a seguir. Substituir `<object-ID>` pela ID de objeto da entidade de serviço. Substitua `<subscription-ID>` pelo nome ou ID da sua assinatura do Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

    ```azurepowershell
    New-AzRoleAssignment --ObjectId <object-ID> --RoleDefinitionName "Contributor" -Scope /subscriptions/<subscription-ID>
    ```

    ---

1. Para gerar uma chave em um Azure Key Vault existente, use um dos comandos a seguir. Substitua `<keyvault-name>` pelo nome do cofre de chaves. Substitua `<key-name>` pelo nome a ser usado para a chave:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az keyvault key create --vault-name <keyvault-name> --name <key-name> --protection software
    ```

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

    ```azurepowershell
    Add-AzKeyVaultKey -VaultName <keyvault-name> -Name <key-name> -Destination 'Software'
    ```
    --- 

__Para adicionar uma política de acesso ao cofre de chaves, use os seguintes comandos__:

1. Para obter a ID do objeto do aplicativo do Azure Cosmos DB, use o comando a seguir. O valor pode ser diferente para cada uma das suas assinaturas do Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

    ```azurepowershell
    Get-AzADServicePrincipal --DisplayName "Azure Cosmos DB" | select-object DisplayName, Id
    ```
    ---

    Esse comando retorna a ID de objeto, que é um GUID. Salve-o para mais tarde

1. Para definir a política, use o comando a seguir. Substitua `<keyvault-name>` pelo nome do Azure Key Vault existente. Substitua `<object-ID>` pelo GUID da etapa anterior:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)
    
    ```azurepowershell
    Set-AzKeyVaultAccessPolicy -VaultName <keyvault-name> -ObjectId <object-ID> -PermissionsToKeys get, unwrapKey, wrapKey
    ```
    ---    

__Para obter os valores__ para a `cmk_keyvault` (ID do Key Vault) e os parâmetros `resource_cmk_uri` (URI de chave) necessários para este modelo, use as seguintes etapas:

1. Para obter a ID do Key Vault, use o seguinte comando:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az keyvault show --name <keyvault-name> --query 'id' --output tsv
    ```

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

    ```azurepowershell
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'
    ```
    ---

    Esse comando retorna um valor semelhante a `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>`.

1. Para obter o valor do URI para a chave gerenciada pelo cliente, use o seguinte comando:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv
    ```

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

    ```azurepowershell
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>'
    ```
    ---

    Esse comando retorna um valor semelhante a `https://mykeyvault.vault.azure.net/keys/mykey/{guid}`.

> [!IMPORTANT]
> Depois que um workspace tiver sido criado, você não poderá alterar as configurações de dados confidenciais, criptografia, ID do cofre de chaves e identificadores de chave. Para alterar esses valores, você precisa criar um workspace usando os novos valores.

Depois de concluir com êxito as etapas acima, implante o modelo como faria normalmente. Para habilitar o uso de chaves gerenciadas pelo cliente, defina os seguintes parâmetros:

* **Encryption_status** **habilitado**.
* **cmk_keyvault** ao `cmk_keyvault` valor obtido nas etapas anteriores.
* **resource_cmk_uri** ao `resource_cmk_uri` valor obtido nas etapas anteriores.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

Ao usar uma chave gerenciada pelo cliente, Azure Machine Learning cria um grupo de recursos secundário que contém a instância de Cosmos DB. Para obter mais informações, consulte [criptografia em repouso-Cosmos DB](concept-enterprise-security.md#encryption-at-rest).

Uma configuração adicional que você pode fornecer para seus dados é definir o parâmetro **confidential_data** como **true**. Fazendo isso, o faz o seguinte:

* Inicia a criptografia do disco de rascunho local para Azure Machine Learning clusters de computação, fornecendo que você não criou nenhum cluster anterior em sua assinatura. Se você tiver criado anteriormente um cluster na assinatura, abra um tíquete de suporte para que a criptografia do disco de rascunho esteja habilitada para seus clusters de computação.
* Limpa o disco de rascunho local entre execuções.
* Transmite com segurança as credenciais para a conta de armazenamento, o registro de contêiner e a conta SSH da camada de execução para seus clusters de computação usando o Key Vault.
* Permite a filtragem de IP para garantir que os pools de lote subjacentes não possam ser chamados por quaisquer serviços externos diferentes de AzureMachineLearningService.

  Para obter mais informações, consulte [criptografia em repouso](concept-enterprise-security.md#encryption-at-rest).

## <a name="deploy-workspace-behind-a-virtual-network"></a>Implantar espaço de trabalho por trás de uma rede virtual

Ao definir o `vnetOption` valor do parâmetro como `new` ou `existing` , você poderá criar os recursos usados por um espaço de trabalho por trás de uma rede virtual.

> [!IMPORTANT]
> Para o registro de contêiner, somente o SKU ' Premium ' tem suporte.

> [!IMPORTANT]
> Application Insights não oferece suporte à implantação por trás de uma rede virtual.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>Implantar apenas espaço de trabalho por trás do ponto de extremidade privado

Se os recursos associados não estiverem atrás de uma rede virtual, você poderá definir o parâmetro **privateEndpointType** como `AutoAproval` ou `ManualApproval` para implantar o espaço de trabalho por trás de um ponto de extremidade privado.

> [!IMPORTANT]
> A implantação só é válida em regiões que dão suporte a pontos de extremidade privados.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>Usar uma nova rede virtual

Para implantar um recurso por trás de uma nova rede virtual, defina **vnetOption** como **novo** junto com as configurações de rede virtual para o respectivo recurso. A implantação abaixo mostra como implantar um espaço de trabalho com o recurso de conta de armazenamento por trás de uma nova rede virtual.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

Como alternativa, você pode implantar vários ou todos os recursos dependentes por trás de uma rede virtual.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>Usar uma rede virtual existente & recursos

Para implantar um espaço de trabalho com recursos associados existentes, você precisa definir o parâmetro **vnetOption** como **existente** junto com os parâmetros de sub-rede. No entanto, você precisa criar pontos de extremidade de serviço na rede virtual para cada um dos recursos **antes** da implantação. Assim como ocorre com novas implantações de rede virtual, você pode ter um ou todos os seus recursos por trás de uma rede virtual.

> [!IMPORTANT]
> A sub-rede deve ter um `Microsoft.Storage` ponto de extremidade de serviço

> [!IMPORTANT]
> As sub-redes não permitem a criação de pontos de extremidade privados. Desabilite o ponto de extremidade privado para habilitar a sub-rede.

1. Habilite os pontos de extremidade de serviço para os recursos.

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. Implantar o espaço de trabalho

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Siga as etapas em [Implantar recursos do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando chegar à tela __selecionar um modelo__ , escolha o modelo **201-Machine-Learning-Advanced** no menu suspenso.
1. Selecione __selecionar modelo__ para usar o modelo. Forneça as seguintes informações necessárias e quaisquer outros parâmetros, dependendo do cenário de implantação.

   * Assinatura: Selecione a assinatura do Azure a ser usada para esses recursos.
   * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
   * Região: selecione a região do Azure em que os recursos serão criados.
   * Nome do workspace: O nome a ser usado para o workspace do Azure Machine Learning que será criado. O nome do workspace deverá ter entre 3 e 33 caracteres. E o nome poderá conter apenas caracteres alfanuméricos e '-'.
   * Localização: Selecione a localização onde os recursos serão criados.
1. Selecione __Examinar + criar__.
1. Na tela __revisar + criar__ , concorde com os termos e condições listados e selecione __criar__.

Para obter mais informações, consulte [Implantar recursos de modelo personalizado](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso do Azure Key Vault e modelos do Azure Resource Manager

Quando você usa um modelo de Azure Resource Manager para criar o workspace e os recursos associados (incluindo o Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implantação e integração contínuas.

A maioria das operações de criação de recursos por meio de modelos é idempotente, mas o Key Vault limpa as políticas de acesso toda vez que o modelo é usado. Limpar as políticas de acesso interrompe o acesso ao Key Vault para qualquer workspace existente que o esteja usando. Por exemplo, as ações de parar/criar funcionalidades de VM do Azure Notebooks podem falhar.  

Para evitar esse problema, recomendamos uma das seguintes abordagens:

* Não implante o modelo mais de uma vez para os mesmos parâmetros. Ou exclua os recursos existentes antes de usar o modelo para recriá-los.

* Examine as políticas de acesso do Key Vault e use essas políticas para definir a propriedade `accessPolicies` do modelo. Para exibir as políticas de acesso, use o seguinte comando da CLI do Azure:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Para obter mais informações sobre como usar a seção `accessPolicies` do modelo, consulte a [referência de objeto AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Verifique se o recurso do Key Vault já existe. Se existir, não o recrie por meio do modelo. Por exemplo, para usar o Key Vault existente em vez de criar outro, faça as seguintes alterações no modelo:

    * **Adicione** um parâmetro que aceita a ID de um recurso do Key Vault existente:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Remova** a seção que cria um recurso do Key Vault:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Remova** linha `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` da seção `dependsOn` do workspace. Além disso **altere** a entrada `keyVault` na seção `properties` do workspace para referenciar o parâmetro `keyVaultId`:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Após essas alterações, você pode especificar a ID do recurso do Key Vault existente ao executar o modelo. Em seguida, o modelo reutilizará o Key Vault definindo a propriedade `keyVault` do workspace como a ID desse Key Vault.

    Para obter a ID do Key Vault, você pode fazer referência à saída da execução do modelo original ou usar o CLI do Azure. O seguinte comando é um exemplo de como usar a CLI do Azure para obter a ID de recurso do Key Vault:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Esse comando retorna um valor semelhante ao texto a seguir:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Próximas etapas

* [Implantar recursos com modelos do Resource Manager e API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md).
* [Criar e implantar grupos de recursos do Azure por meio do Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
