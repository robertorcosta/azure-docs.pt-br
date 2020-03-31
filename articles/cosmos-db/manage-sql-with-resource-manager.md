---
title: Criar e gerenciar o Azure Cosmos DB com modelos do Gerenciador de Recursos
description: Use modelos do Azure Resource Manager para criar e configurar o Api SQL (Core) do AQE
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 72a87c3b23e0eed6cfbf1614388702443f4e99d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251837"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Gerencie os recursos de API do Azure Cosmos DB SQL (Core) com modelos do Azure Resource Manager

Neste artigo, você aprenderá a usar os modelos do Azure Resource Manager para ajudar a automatizar o gerenciamento de suas contas, bancos de dados e contêineres do Azure Cosmos DB.

Este artigo mostra apenas exemplos de modelo do Azure Resource Manager para contas API SQL. Você também pode encontrar exemplos de modelos para [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md)e APIs [de tabela.](manage-table-with-resource-manager.md)

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Crie uma conta, banco de dados e contêiner do Azure Cosmos

O modelo a seguir do Azure Resource Manager cria uma conta do Azure Cosmos com:

* Dois contêineres que compartilham 400 Unidades Solicitadas por segundo (RU/s) no nível do banco de dados.
* Um recipiente com throughput dedicado de 400 RU/s.

Para criar os recursos do Azure Cosmos DB, copie o modelo de exemplo a seguir e implante-o conforme descrito, seja via [PowerShell](#deploy-via-powershell) ou [Azure CLI](#deploy-via-azure-cli).

* Opcionalmente, você pode visitar a [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) e implantar o modelo no portal Azure.
* Você também pode baixar o modelo para o seu computador local `--template-file` ou criar um novo modelo e especificar o caminho local com o parâmetro.

> [!IMPORTANT]
>
> * Quando você adiciona ou remove locais em uma conta do Azure Cosmos, você não pode modificar simultaneamente outras propriedades. Essas operações devem ser feitas separadamente.
> * Os nomes das contas são limitados a 44 caracteres, todos minúsculos.
> * Para alterar os valores de throughput, envie o modelo com RU/s atualizados.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Para criar um contêiner com grande chave de `"version":2` partição, `partitionKey` modifique o modelo anterior para incluir a propriedade dentro do objeto.

### <a name="deploy-via-powershell"></a>Implantar via PowerShell

Para usar o PowerShell para implantar o modelo do Azure Resource Manager:

1. **Copie** o roteiro.
2. Selecione **Tente** abrir o Azure Cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e, em seguida, **selecione Colar**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Você pode optar por implantar o modelo com uma versão instalada localmente do PowerShell em vez do Azure Cloud Shell. Você precisará [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Corra `Get-Module -ListAvailable Az` para encontrar a versão necessária.

### <a name="deploy-via-azure-cli"></a>Implantar via Cli do Azure

Para usar o Azure CLI para implantar o modelo do Azure Resource Manager:

1. **Copie** o roteiro.
2. Selecione **Tente** abrir o Azure Cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e, em seguida, **selecione Colar**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta recém-criada do Azure Cosmos depois de provisionada. Você pode optar por implantar o modelo com uma versão instalada localmente do Azure CLI em vez do Azure Cloud Shell. Para obter mais informações, consulte o artigo [CLI (Command-Line Interface, interface de linha de comando do Azure).](/cli/azure/)

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Crie um contêiner Azure Cosmos DB com funcionalidade do lado do servidor

Você pode usar um modelo do Azure Resource Manager para criar um contêiner Azure Cosmos DB com um procedimento armazenado, gatilho e função definida pelo usuário.

Copie o modelo de exemplo a seguir e implante-o conforme descrito, seja com [powershell](#deploy-with-powershell) ou [azure CLI](#deploy-with-azure-cli).

* Opcionalmente, você pode visitar [a Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) e implantar o modelo no portal Azure.
* Você também pode baixar o modelo para o seu computador local `--template-file` ou criar um novo modelo e especificar o caminho local com o parâmetro.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Implantar com o PowerShell

Para usar o PowerShell para implantar o modelo do Azure Resource Manager:

1. **Copie** o roteiro.
1. Selecione **Tente** abrir o Azure Cloud Shell.
1. Clique com o botão direito do mouse na janela Azure Cloud Shell e, em seguida, **selecione Colar**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Você pode optar por implantar o modelo com uma versão instalada localmente do PowerShell em vez do Azure Cloud Shell. Você precisará [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Corra `Get-Module -ListAvailable Az` para encontrar a versão necessária.

### <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

Para usar o Azure CLI para implantar o modelo do Azure Resource Manager:

1. **Copie** o roteiro.
2. Selecione **Tente** abrir o Azure Cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e, em seguida, **selecione Colar**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

* [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
* [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Solucionar problemas comuplediaerros comuns do Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
