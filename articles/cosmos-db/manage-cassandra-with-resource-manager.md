---
title: Modelos de gerenciador de recursos para API AZURE Cosmos DB Cassandra
description: Use modelos do Azure Resource Manager para criar e configurar a API Azure Cosmos DB Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251889"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gerencie os recursos da API do Azure Cosmos DB Cassandra usando modelos do Azure Resource Manager

Este artigo descreve como executar diferentes operações para automatizar o gerenciamento de suas contas, bancos de dados e contêineres do Azure Cosmos usando modelos do Azure Resource Manager. Este artigo tem exemplos apenas para contas API SQL, para encontrar exemplos para outras contas do tipo API ver: use modelos do Azure Resource Manager com a API do Azure Cosmos DB para [Artigos sql,](manage-sql-with-resource-manager.md) [gremlin,](manage-gremlin-with-resource-manager.md) [mongodb,](manage-mongodb-with-resource-manager.md) [tabela.](manage-table-with-resource-manager.md)

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Criar conta, espaço de chave e tabela do Azure Cosmos<a id="create-resource"></a>

Crie recursos do Azure Cosmos DB usando um modelo do Azure Resource Manager. Este modelo criará uma conta do Azure Cosmos para a API cassandra com duas tabelas que compartilham o throughput de 400 RU/s no nível de espaço-chave. Copie o modelo e implante conforme mostrado abaixo ou visite [a Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) e implante no portal Azure. Você também pode baixar o modelo para o seu computador local `--template-file` ou criar um novo modelo e especificar o caminho local com o parâmetro.

> [!NOTE]
> Os nomes das contas devem ser minúsculos e 44 ou menos caracteres.
> Para atualizar RU/s, envie o modelo com valores de propriedade de throughput atualizados.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Implantar com o Cli Azure

Para implantar o modelo do Azure Resource Manager usando o Azure CLI, **copie** o script e **selecione Tente** abrir o Azure Cloud Shell. Para colar o script, clique com o botão direito do mouse na concha e, em seguida, **selecione Colar**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta recém-criada do Azure Cosmos depois de ser provisionada. Se você optar por usar uma versão instalada localmente do Azure CLI em vez de usar o Cloud Shell, consulte o artigo [do Azure CLI.](/cli/azure/)


## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Solucionar problemas comuplediaerros comuns do Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
