---
title: Modelos de gerenciador de recursos para API DB do Azure Cosmos para MongoDB
description: Use os modelos do Azure Resource Manager para criar e configurar a API do Azure Cosmos DB para O MongoDB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 531f122679c463b11c84eba2fca9f30b09e0935f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063630"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gerencie os recursos da API do Azure Cosmos DB MongoDB usando modelos do Azure Resource Manager

Este artigo descreve como executar diferentes operações para automatizar o gerenciamento de suas contas, bancos de dados e contêineres do Azure Cosmos usando modelos do Azure Resource Manager. Este artigo tem exemplos para a API do Azure Cosmos DB apenas para O MongoDB, para encontrar exemplos para outras contas do tipo API ver: use modelos do Azure Resource Manager com a API do Azure Cosmos DB para [cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [SQL](manage-sql-with-resource-manager.md), artigos [de tabela.](manage-table-with-resource-manager.md)

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Crie a API do Azure Cosmos DB para conta, banco de dados e coleta do MongoDB<a id="create-resource"></a>

Crie recursos do Azure Cosmos DB usando um modelo do Azure Resource Manager. Este modelo criará uma conta do Azure Cosmos para a API do MongoDB com duas coleções que compartilham o throughput de 400 RU/s no nível do banco de dados. Copie o modelo e implante conforme mostrado abaixo ou visite [a Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) e implante no portal Azure. Você também pode baixar o modelo para o seu computador local `--template-file` ou criar um novo modelo e especificar o caminho local com o parâmetro.

> [!NOTE]
> Os nomes das contas devem ser minúsculos e 44 ou menos caracteres.
> Para atualizar RU/s, envie o modelo com valores de propriedade de throughput atualizados.
>
> Atualmente, você só pode criar a versão 3.2 (ou `*.documents.azure.com`seja, contas usando o ponto final no formato ) da API do Azure Cosmos DB para contas MongoDB usando PowerShell e CLI. Para criar a versão 3.6 das contas, use modelos do Gerenciador de Recursos (abaixo) ou portal Azure.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

### <a name="deploy-via-the-azure-cli"></a>Implantar através do Azure CLI

Para implantar o modelo do Azure Resource Manager usando o Azure CLI, **copie** o script e **selecione Tente** abrir o Azure Cloud Shell. Para colar o script, clique com o botão direito do mouse na concha e, em seguida, **selecione Colar**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta recém-criada do Azure Cosmos depois de ser provisionada. Se você optar por usar uma versão instalada localmente do Azure CLI em vez de usar o Cloud Shell, consulte o artigo [do Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Solucionar problemas comuplediaerros comuns do Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
