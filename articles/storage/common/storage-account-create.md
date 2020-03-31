---
title: Criar uma conta de armazenamento
titleSuffix: Azure Storage
description: Aprenda a criar uma conta de armazenamento usando o portal Azure, o Azure PowerShell ou o Azure CLI. Uma conta de armazenamento Do Zure fornece um namespace exclusivo no Microsoft Azure para armazenar e acessar seus dados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c2d1e8b4975be0657983192df00cc434da00a6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255399"
---
# <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para os dados do Azure Storage que são acessíveis de qualquer lugar do mundo em HTTP ou HTTPS. Os dados em sua conta de armazenamento Azure são duráveis e altamente disponíveis, seguros e maciçamente escaláveis.

Neste artigo de como fazer, você aprende a criar uma conta de armazenamento usando o [portal Azure,](https://portal.azure.com/) [Azure PowerShell,](https://docs.microsoft.com/powershell/azure/overview) [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)ou um [modelo do Azure Resource Manager.](../../azure-resource-manager/management/overview.md)  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Para criar uma conta de armazenamento Azure com o PowerShell, certifique-se de ter instalado o módulo Azure PowerShell versão 0.7 ou posterior. Para obter mais informações, consulte [Introduzindo o módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Para encontrar sua versão atual, execute o seguinte comando:

```powershell
Get-InstalledModule -Name "Az"
```

Para instalar ou atualizar o Azure PowerShell, consulte [Instalar o módulo Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Você pode entrar no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir:

- Você pode executar comandos CLI de dentro do portal Azure, no Azure Cloud Shell.
- Você pode instalar o CLI e executar comandos CLI localmente.

### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. O Azure CLI está pré-instalado e configurado para uso com sua conta. Clique no botão **Cloud Shell** no menu na seção superior direita do portal Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão lança uma concha interativa que você pode usar para executar as etapas descritas neste artigo de como fazer:

[![Captura de tela mostrando a janela Cloud Shell no portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Você também pode instalar e usar a CLI do Azure localmente. Este artigo de como fazer requer que você esteja executando a versão 2.0.4 ou posterior do Azure CLI. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

# <a name="template"></a>[Modelo](#tab/template)

Nenhum.

---

## <a name="sign-in-to-azure"></a>Entrar no Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Faça login no [portal Azure](https://portal.azure.com).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para lançar o Azure Cloud Shell, entre no [portal Azure](https://portal.azure.com).

Para fazer login na instalação local da CLI, execute o comando [az login:](/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Agora você está pronto para criar uma conta de armazenamento.

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

Uma conta de armazenamento de **uso geral v2** fornece acesso a todos os serviços de Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. As etapas descritas aqui criam uma conta de armazenamento v2 de uso geral, mas as etapas para criar qualquer tipo de conta de armazenamento são semelhantes.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Primeiro, crie um grupo de recursos com o PowerShell usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Caso não tenha certeza de qual região especificar para o parâmetro `-Location`, recupere uma lista de regiões com suporte para sua assinatura com o comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
```

Em seguida, crie uma conta de armazenamento v2 de uso geral com o RA-GRS (Read-Access geo-redundante) usando o comando [New-AzStorageAccount.](/powershell/module/az.storage/New-azStorageAccount) Lembre-se de que o nome da sua conta de armazenamento deve ser único em todo o Azure, então substitua o valor do espaço reservado entre parênteses pelo seu próprio valor único:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Se você planeja usar [o Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)inclua `-EnableHierarchicalNamespace $True` nesta lista de parâmetros.

Para criar uma conta de armazenamento v2 de uso geral com uma opção de replicação diferente, substitua o valor desejado na tabela abaixo pelo parâmetro **SkuName.**

|Opção de replicação  |Parâmetro SkuName  |
|---------|---------|
|Armazenamento com redundância local (LRS)     |Standard_LRS         |
|Armazenamento com redundância de zona (ZRS)     |Standard_ZRS         |
|Armazenamento com redundância geográfica (GRS)     |Standard_GRS         |
|Armazenamento com redundância geográfica com acesso de leitura (GRS)     |Standard_RAGRS         |
|Armazenamento redundante em geo-zona (GZRS) (visualização)    |Standard_GZRS         |
|Armazenamento de acesso de leitura de geozona-redundante (RA-GZRS) (visualização)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Primeiro, crie criar um novo grupo de recursos com a CLI do Azure usando o comando [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Se não tiver certeza de qual região especificar para o parâmetro `--location`, recupere uma lista de regiões com suporte para a assinatura com o comando [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Em seguida, crie uma conta de armazenamento v2 de uso geral com armazenamento geo-redundante de acesso de leitura usando o comando [az storage create.](/cli/azure/storage/account#az_storage_account_create) Lembre-se de que o nome da sua conta de armazenamento deve ser único em todo o Azure, então substitua o valor do espaço reservado entre parênteses pelo seu próprio valor único:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Se você planeja usar [o Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)inclua `--enable-hierarchical-namespace true` nesta lista de parâmetros. 

Para criar uma conta de armazenamento v2 de uso geral com uma opção de replicação diferente, substitua o valor desejado na tabela abaixo pelo parâmetro **sku.**

|Opção de replicação  |Parâmetro sku  |
|---------|---------|
|Armazenamento com redundância local (LRS)     |Standard_LRS         |
|Armazenamento com redundância de zona (ZRS)     |Standard_ZRS         |
|Armazenamento com redundância geográfica (GRS)     |Standard_GRS         |
|Armazenamento com redundância geográfica com acesso de leitura (GRS)     |Standard_RAGRS         |
|Armazenamento redundante em geo-zona (GZRS) (visualização)    |Standard_GZRS         |
|Armazenamento de acesso de leitura de geozona-redundante (RA-GZRS) (visualização)    |Standard_RAGZRS         |

# <a name="template"></a>[Modelo](#tab/template)

Você pode usar o Azure PowerShell ou a CLI do Azure para implantar um modelo do Resource Manager para criar uma conta de armazenamento. O modelo usado neste artigo de como fazer é do [Azure Resource Manager quickstart templates](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Para executar os scripts, selecione **Experimentar** para abrir o Azure Cloud Shell. Para colar o script, clique com o botão direito do mouse no shell e, em seguida, selecione **Colar**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Este modelo serve apenas como exemplo. Existem muitas configurações de conta de armazenamento que não estão configuradas como parte deste modelo. Por exemplo, se você quiser usar [o Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/) `StorageAccountPropertiesCreateParameters` você `true`modificará este modelo definindo a `isHnsEnabledad` propriedade do objeto como . 

Para saber como modificar esse modelo ou criar novos, consulte:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- [Referência de modelo da conta de armazenamento](/azure/templates/microsoft.storage/allversions).
- [Exemplos de modelo de conta de armazenamento adicional](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Para saber mais sobre as opções de replicação disponíveis , veja [Opções de replicação de armazenamento](storage-redundancy.md).

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento

A exclusão de uma conta de armazenamento exclui toda a conta, incluindo todos os dados da conta, e não pode ser desfeita.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue até a conta de armazenamento no [portal Azure](https://portal.azure.com).
1. Clique em **Excluir**.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Para excluir a conta de armazenamento, use o comando [Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para excluir a conta de armazenamento, use o comando [az storage account delete:](/cli/azure/storage/account#az-storage-account-delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Modelo](#tab/template)

Para excluir a conta de armazenamento, use o Azure PowerShell ou o Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Alternativamente, você pode excluir o grupo de recursos, que exclui a conta de armazenamento e quaisquer outros recursos nesse grupo de recursos. Para obter mais informações sobre a exclusão de um grupo de recursos, consulte [Excluir grupo de recursos e recursos](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Não é possível restaurar uma conta de armazenamento excluída nem recuperar nenhuma parte de seu conteúdo antes da exclusão. Não se esqueça de fazer backup de todas as informações que você deseja salvar antes de excluir a conta. Isso também é verdadeiro para todos os recursos na conta – depois que você excluir um blob, tabela, fila ou arquivo, ele será excluído permanentemente.
>
> Se você tentar excluir uma conta de armazenamento associada a uma máquina virtual do Azure, poderá receber um erro indicando que a conta de armazenamento ainda está em uso. Para ajudar a solucionar esse erro, consulte [Solucionar erros ao excluir contas de armazenamento](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Próximas etapas

Neste artigo de como fazer, você criou uma conta de armazenamento padrão V2 de uso geral. Para aprender como carregar e baixar blobs de e para sua conta de armazenamento, continue a um dos quickstarts de armazenamento Blob.

# <a name="portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando o PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando a CLI do Azure](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[Modelo](#tab/template)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

---
