---
title: Modelos do Resource Manager para Azure Cosmos DB API do Cassandra
description: Use modelos de Azure Resource Manager para criar e configurar Azure Cosmos DB API do Cassandra.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: ff75597bece386635195a84572a9f07b04d9c60f
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200796"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gerenciar Azure Cosmos DB API do Cassandra recursos usando modelos de Azure Resource Manager

Este artigo descreve como executar operações diferentes para automatizar o gerenciamento de suas contas de Azure Cosmos DB, bancos de dados e contêineres usando modelos de Azure Resource Manager. Este artigo tem exemplos somente para contas de API do Cassandra, para encontrar exemplos para outras contas de tipo de API, consulte: usar modelos de Azure Resource Manager com a API de Azure Cosmos DB para [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), artigos de [tabela](manage-table-with-resource-manager.md) .

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Criar conta do Azure Cosmos, espaço de keyspace e tabela<a id="create-resource"></a>

Crie Azure Cosmos DB recursos usando um modelo de Azure Resource Manager. Este modelo criará uma conta do Azure Cosmos para API do Cassandra com duas tabelas que compartilham a taxa de transferência de 400 RU/s no nível de keyspace. Copie o modelo e implante-o conforme mostrado abaixo ou visite a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) e implante do portal do Azure. Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o `--template-file` parâmetro.

> [!NOTE]
> Os nomes de conta devem ser minúsculos e 44 ou menos caracteres.
> Para atualizar RU/s, envie novamente o modelo com valores de propriedade de produtividade atualizados.

```json
{
"$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "accountName": {
        "type": "string",
        "defaultValue": "",
        "metadata": {
            "description": "Cosmos DB account name, max length 44 characters"
        }
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
            "description": "Location for the Cosmos DB account."
        }
    },
    "primaryRegion":{
        "type":"string",
        "metadata": {
            "description": "The primary replica region for the Cosmos DB account."
        }
    },
    "secondaryRegion":{
        "type":"string",
        "metadata": {
          "description": "The secondary replica region for the Cosmos DB account."
      }
    },
    "defaultConsistencyLevel": {
        "type": "string",
        "defaultValue": "Session",
        "allowedValues": [ "Eventual", "ConsistentPrefix", "Session", "BoundedStaleness", "Strong" ],
        "metadata": {
            "description": "The default consistency level of the Cosmos DB account."
        }
    },
    "maxStalenessPrefix": {
        "type": "int",
        "defaultValue": 100000,
        "minValue": 10,
        "maxValue": 1000000,
        "metadata": {
            "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
        }
    },
    "maxIntervalInSeconds": {
        "type": "int",
        "defaultValue": 300,
        "minValue": 5,
        "maxValue": 86400,
        "metadata": {
            "description": "Max lag time (seconds). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
        }
    },
    "automaticFailover": {
        "type": "bool",
        "defaultValue": true,
        "allowedValues": [ true, false ],
        "metadata": {
            "description": "Enable automatic failover for regions"
        }
    },
    "keyspaceName": {
        "type": "string",
        "defaultValue": "Keyspace1",
        "metadata": {
            "description": "The name for the Cassandra Keyspace"
        }
    },
    "tableName": {
        "type": "string",
        "defaultValue": "Table1",
        "metadata": {
            "description": "The name for the Cassandra table"
        }
    },
    "throughput": {
        "type": "int",
        "defaultValue": 400,
        "minValue": 400,
        "maxValue": 1000000,
        "metadata": {
            "description": "The throughput for the Cassandra table"
        }
    }
},
"variables": {
    "accountName": "[toLower(parameters('accountName'))]",
    "consistencyPolicy": {
        "Eventual": {
            "defaultConsistencyLevel": "Eventual"
        },
        "ConsistentPrefix": {
            "defaultConsistencyLevel": "ConsistentPrefix"
        },
        "Session": {
            "defaultConsistencyLevel": "Session"
        },
        "BoundedStaleness": {
            "defaultConsistencyLevel": "BoundedStaleness",
            "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
            "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
        },
        "Strong": {
            "defaultConsistencyLevel": "Strong"
        }
    },
    "locations":
    [
        {
            "locationName": "[parameters('primaryRegion')]",
            "failoverPriority": 0,
            "isZoneRedundant": false
        },
        {
            "locationName": "[parameters('secondaryRegion')]",
            "failoverPriority": 1,
            "isZoneRedundant": false
        }
    ]
},
"resources":
[
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-03-01",
        "location": "[parameters('location')]",
        "kind": "GlobalDocumentDB",
        "properties": {
            "capabilities": [{ "name": "EnableCassandra" }],
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]"
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces",
        "name": "[concat(variables('accountName'), '/', parameters('keyspaceName'))]",
        "apiVersion": "2020-03-01",
        "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/', variables('accountName'))]" ],
        "properties":{
            "resource":{
                "id": "[parameters('keyspaceName')]"
            }
        }
    },
    {
        "type": "Microsoft.DocumentDb/databaseAccounts/cassandraKeyspaces/tables",
        "name": "[concat(variables('accountName'), '/', parameters('keyspaceName'), '/', parameters('tableName'))]",
        "apiVersion": "2020-03-01",
        "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces', variables('accountName'), parameters('keyspaceName'))]" ],
        "properties":
        {
            "resource":{
                "id":  "[parameters('tableName')]",
                "schema": {
                    "columns": [
                        { "name": "loadid", "type": "uuid" },
                        { "name": "machine", "type": "uuid" },
                        { "name": "cpu", "type": "int" },
                        { "name": "mtime", "type": "int" },
                        { "name": "load", "type": "float" }
                    ],
                    "partitionKeys": [
                        { "name": "machine" },
                        { "name": "cpu" },
                        { "name": "mtime" }
                    ],
                    "clusterKeys": [
                        { "name": "loadid", "orderBy": "asc" }
                    ]
                },
                "options": { "throughput": "[parameters('throughput')]" }
            }
        }
    }
]
}
```

## <a name="deploy-with-the-azure-cli"></a>Implantar com o CLI do Azure

Para implantar o modelo de Azure Resource Manager usando o CLI do Azure, **Copie** o script e selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the table name: ' tableName
read -p 'Enter the throughput: ' throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keyspaceName=$keyspaceName \
   tableName=$tableName throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta recém-criada do Azure Cosmos depois que ela é provisionada. Se você optar por usar uma versão instalada localmente do CLI do Azure em vez de usar Cloud Shell, consulte o artigo [CLI do Azure](/cli/azure/) .

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Solucionar erros comuns de implantação de Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
