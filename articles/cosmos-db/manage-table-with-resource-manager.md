---
title: Modelos de gerenciador de recursos para API de tabela do Azure Cosmos DB
description: Use os modelos do Azure Resource Manager para criar e configurar a API de tabela do Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246702"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gerenciar os recursos da API da tabela Azure Cosmos DB usando modelos do Azure Resource Manager

Este artigo descreve como executar diferentes operações para automatizar o gerenciamento de suas contas, bancos de dados e contêineres do Azure Cosmos usando modelos do Azure Resource Manager. Este artigo tem exemplos apenas para contas de API de tabela, para encontrar exemplos para outras contas do tipo API ver: use modelos do Azure Resource Manager com a API do Azure Cosmos DB para artigos [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB,](manage-mongodb-with-resource-manager.md) [SQL.](manage-sql-with-resource-manager.md)

## <a name="create-azure-cosmos-account-and-table"></a>Criar conta e tabela do Azure Cosmos<a id="create-resource"></a>

Crie recursos do Azure Cosmos DB usando um modelo do Azure Resource Manager. Este modelo criará uma conta do Azure Cosmos para a API de tabela com uma tabela a 400 RU/s throughput. Copie o modelo e implante conforme mostrado abaixo ou visite [a Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) e implante no portal Azure. Você também pode baixar o modelo para o seu computador local `--template-file` ou criar um novo modelo e especificar o caminho local com o parâmetro.

> [!NOTE]
> Os nomes das contas devem ser minúsculos e 44 ou menos caracteres.
> Para atualizar RU/s, envie o modelo com valores de propriedade de throughput atualizados.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Implantar via PowerShell

Para implantar o modelo resource manager usando o PowerShell, **copie** o script e **selecione Tente** abrir o Azure Cloud Shell. Para colar o script, clique com o botão direito do mouse na concha e, em seguida, **selecione Colar**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Se você optar por usar uma versão instalada localmente do PowerShell em vez do shell do Azure Cloud, você terá que [instalar](/powershell/azure/install-az-ps) o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para encontrar a versão.

### <a name="deploy-via-the-azure-cli"></a>Implantar através do Azure CLI

Para implantar o modelo do Azure Resource Manager usando o Azure CLI, **copie** o script e **selecione Tente** abrir o Azure Cloud Shell. Para colar o script, clique com o botão direito do mouse na concha e, em seguida, **selecione Colar**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta recém-criada do Azure Cosmos depois de ser provisionada. Se você optar por usar uma versão instalada localmente do Azure CLI em vez de usar o Cloud Shell, consulte o artigo [do Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Solucionar problemas comuplediaerros comuns do Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)