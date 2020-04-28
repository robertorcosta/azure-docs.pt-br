---
title: Criar e gerenciar Azure Cosmos DB com modelos do Resource Manager
description: Usar modelos de Azure Resource Manager para criar e configurar o Azure Cosmos DB para a API do SQL (Core)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: mjbrown
ms.openlocfilehash: dc4f4666df4d3183e27f073e9ebbe8528cfbe262
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188783"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Gerenciar recursos de API do Azure Cosmos DB SQL (núcleo) com modelos de Azure Resource Manager

Neste artigo, você aprenderá a usar os modelos do Azure Resource Manager para ajudar a automatizar o gerenciamento de suas contas, bancos de dados e contêineres do Azure Cosmos DB.

Este artigo mostra apenas Azure Resource Manager exemplos de modelo para contas da API do SQL. Você também pode encontrar exemplos de modelo para [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)e APIs de [tabela](manage-table-with-resource-manager.md) .

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Criar uma conta, um banco de dados e um contêiner do Azure Cosmos

O modelo a seguir Azure Resource Manager cria uma conta do Azure cosmos com:

* Dois contêineres que compartilham a taxa de transferência de 400 unidades solicitadas por segundo (RU/s) no nível do banco de dados.
* Um contêiner com taxa de transferência dedicada de 400 RU/s.

Para criar os recursos de Azure Cosmos DB, copie o modelo de exemplo a seguir e implante-o conforme descrito, seja por meio do [PowerShell](#deploy-via-powershell) ou [CLI do Azure](#deploy-via-azure-cli).

* Opcionalmente, você pode visitar a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) e implantar o modelo do portal do Azure.
* Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o `--template-file` parâmetro.

> [!IMPORTANT]
>
> * Ao adicionar ou remover locais de uma conta do Azure Cosmos, você não pode modificar outras propriedades simultaneamente. Essas operações devem ser feitas separadamente.
> * Os nomes de conta são limitados a 44 caracteres, tudo em minúsculas.
> * Para alterar os valores de taxa de transferência, reimplante o modelo com RU/s atualizado.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('sql-', uniqueString(resourceGroup().id))]",
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
            "minValue": 10,
            "defaultValue": 100000,
            "maxValue": 2147483647,
            "metadata": {
                "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
            }
        },
        "maxIntervalInSeconds": {
            "type": "int",
            "minValue": 5,
            "defaultValue": 300,
            "maxValue": 86400,
            "metadata": {
                "description": "Max lag time (minutes). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
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
        "databaseName": {
            "type": "string",
            "defaultValue": "myDatabase",
            "metadata": {
                "description": "The name for the database"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "myContainer",
            "metadata": {
                "description": "The name for the container"
            }
        },
        "throughput": {
            "type": "int",
            "defaultValue": 400,
            "minValue": 400,
            "maxValue": 1000000,
            "metadata": {
                "description": "The throughput for the container"
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
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
                "locations": "[variables('locations')]",
                "databaseAccountOfferType": "Standard",
                "enableAutomaticFailover": "[parameters('automaticFailover')]"
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
            "properties":{
                "resource":{
                    "id": "[parameters('databaseName')]"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases', variables('accountName'), parameters('databaseName'))]" ],
            "properties":
            {
                "resource":{
                    "id":  "[parameters('containerName')]",
                    "partitionKey": {
                        "paths": [
                        "/myPartitionKey"
                        ],
                        "kind": "Hash"
                    },
                    "indexingPolicy": {
                        "indexingMode": "consistent",
                        "includedPaths": [{
                                "path": "/*"
                            }
                        ],
                        "excludedPaths": [{
                                "path": "/myPathToNotIndex/*"
                            }
                        ],
                        "compositeIndexes":[  
                        [
                            {
                                "path":"/name",
                                "order":"ascending"
                            },
                            {
                                "path":"/age",
                                "order":"descending"
                            }
                        ]
                    ],
                    "spatialIndexes": [
                            {
                                "path": "/path/to/geojson/property/?",
                                "types": [
                                    "Point",
                                    "Polygon",
                                    "MultiPolygon",
                                    "LineString"
                                ]
                            }
                        ]
                    },
                    "defaultTtl": 86400,
                    "uniqueKeyPolicy": {
                        "uniqueKeys": [
                        {
                            "paths": [
                            "/phoneNumber"
                            ]
                        }
                        ]
                    }
                },
                "options": { "throughput": "[parameters('throughput')]" }
            }
        }
    ]
}
```

> [!NOTE]
> Para criar um contêiner com chave de partição grande, modifique o modelo anterior para incluir `"version":2` a propriedade no `partitionKey` objeto.

### <a name="deploy-via-powershell"></a>Implantar por meio do PowerShell

Para usar o PowerShell para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
2. Selecione **experimentar** para abrir Azure cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter the container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2020-03-01" --ResourceGroupName $resourceGroupName).name
```

Você pode optar por implantar o modelo com uma versão instalada localmente do PowerShell em vez de Azure Cloud Shell. Você precisará [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Execute `Get-Module -ListAvailable Az` para localizar a versão necessária.

### <a name="deploy-via-azure-cli"></a>Implantar via CLI do Azure

Para usar CLI do Azure para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
2. Selecione **experimentar** para abrir Azure cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the container throughput: throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   containerName=$containerName \
   throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta recém-criada do Azure Cosmos depois de ser provisionada. Você pode optar por implantar o modelo com uma versão instalada localmente do CLI do Azure em vez Azure Cloud Shell. Para obter mais informações, consulte o artigo da [CLI (interface de linha de comando) do Azure](/cli/azure/) .

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Criar um contêiner de Azure Cosmos DB com a funcionalidade do lado do servidor

Você pode usar um modelo de Azure Resource Manager para criar um contêiner de Azure Cosmos DB com um procedimento armazenado, um gatilho e uma função definida pelo usuário.

Copie o modelo de exemplo a seguir e implante-o conforme descrito, seja com o [PowerShell](#deploy-with-powershell) ou [CLI do Azure](#deploy-with-azure-cli).

* Opcionalmente, você pode visitar a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) e implantar o modelo do portal do Azure.
* Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o `--template-file` parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('cosmos-', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Cosmos DB account name"
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
            "minValue": 10,
            "defaultValue": 100000,
            "maxValue": 2147483647,
            "metadata": {
                "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
            }
        },
        "maxIntervalInSeconds": {
            "type": "int",
            "minValue": 5,
            "defaultValue": 300,
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
        "databaseName": {
            "type": "string",
            "defaultValue": "database1",
            "metadata": {
                "description": "The name for the Core (SQL) database"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "container1",
            "metadata": {
                "description": "The name for the Core (SQL) API container"
            }
        },
        "throughput": {
            "type": "int",
            "defaultValue": 400,
            "minValue": 400,
            "maxValue": 1000000,
            "metadata": {
                "description": "The throughput for the container"
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
                "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
                "locations": "[variables('locations')]",
                "databaseAccountOfferType": "Standard",
                "enableAutomaticFailover": "[parameters('automaticFailover')]"
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
            "properties":{
                "resource":{
                    "id": "[parameters('databaseName')]"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases', variables('accountName'), parameters('databaseName'))]" ],
            "properties":
            {
                "resource":{
                    "id":  "[parameters('containerName')]",
                    "partitionKey": {
                        "paths": ["/myPartitionKey"],
                        "kind": "Hash"
                    },
                    "indexingPolicy": {
                        "indexingMode": "consistent",
                        "includedPaths": [{ "path": "/*" } ],
                        "excludedPaths": [{ "path": "/myPathToNotIndex/*" } ]
                    }
                },
                "options": { "throughput": "[parameters('throughput')]" }
            },
            "resources":
            [
                {
                    "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures",
                    "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'), '/myStoredProcedure')]",
                    "apiVersion": "2020-03-01",
                    "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers', variables('accountName'), parameters('databaseName'), parameters('containerName'))]" ],
                    "properties": {
                        "resource": {
                        "id": "myStoredProcedure",
                        "body": "function () { var context = getContext(); var response = context.getResponse(); response.setBody('Hello, World'); }"
                        }
                    }
                },
                {
                    "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers",
                    "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'), '/myPreTrigger')]",
                    "apiVersion": "2020-03-01",
                    "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers', variables('accountName'), parameters('databaseName'), parameters('containerName'))]" ],
                    "properties": {
                        "resource": {
                        "id": "myPreTrigger",
                        "triggerType": "Pre",
                        "triggerOperation": "Create",
                        "body": "function validateToDoItemTimestamp(){var context=getContext();var request=context.getRequest();var itemToCreate=request.getBody();if(!('timestamp'in itemToCreate)){var ts=new Date();itemToCreate['timestamp']=ts.getTime();}request.setBody(itemToCreate);}"
                        }
                    }
                },
                {
                    "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions",
                    "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'), '/myUserDefinedFunction')]",
                    "apiVersion": "2020-03-01",
                    "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers', variables('accountName'), parameters('databaseName'), parameters('containerName'))]" ],
                    "properties": {
                        "resource": {
                        "id": "myUserDefinedFunction",
                        "body": "function tax(income){if(income==undefined)throw'no input';if(income<1000)return income*0.1;else if(income<10000)return income*0.2;else return income*0.4;}"
                        }
                    }
                }
            ]
        }
    ]
}
```

### <a name="deploy-with-powershell"></a>Implantar com o PowerShell

Para usar o PowerShell para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
1. Selecione **experimentar** para abrir Azure cloud Shell.
1. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter the container throughput (i.e. 400)"

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
    -throughput $throughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2020-03-01" --ResourceGroupName $resourceGroupName).name
```

Você pode optar por implantar o modelo com uma versão instalada localmente do PowerShell em vez de Azure Cloud Shell. Você precisará [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Execute `Get-Module -ListAvailable Az` para localizar a versão necessária.

### <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

Para usar CLI do Azure para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
2. Selecione **experimentar** para abrir Azure cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the throughput: ' throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

<a id="free-tier"></a>

## <a name="create-a-free-tier-azure-cosmos-db-account"></a>Criar uma conta de Azure Cosmos DB de camada gratuita

Você pode usar um modelo de Azure Resource Manager para criar um contêiner de Azure Cosmos DB na camada gratuita com um banco de dados com taxa de transferência de 400 RU/s que pode ser compartilhada com até 25 contêineres.

Copie o modelo de exemplo a seguir e implante-o conforme descrito, seja com o [PowerShell](#deploy-with-powershell) ou [CLI do Azure](#deploy-with-azure-cli).

* Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o `--template-file` parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('cosmos-', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Cosmos DB account name"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the Cosmos DB account."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "database1",
            "metadata": {
                "description": "The name for the Core (SQL) database"
            }
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2020-03-01",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "properties": {
                "enableFreeTier": true,
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "apiVersion": "2020-03-01",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
            "properties": {
                "resource": {
                    "id": "[parameters('databaseName')]"
                },
                "options": {"throughput": 400}
            }
        }
    ]
}
```

### <a name="deploy-with-powershell"></a>Implantar com o PowerShell

Para usar o PowerShell para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
1. Selecione **experimentar** para abrir Azure cloud Shell.
1. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$databaseName = Read-Host -Prompt "Enter the database name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -databaseName $databaseName `

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2020-03-01" --ResourceGroupName $resourceGroupName).name
```

Você pode optar por implantar o modelo com uma versão instalada localmente do PowerShell em vez de Azure Cloud Shell. Você precisará [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Execute `Get-Module -ListAvailable Az` para localizar a versão necessária.

### <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

Para usar CLI do Azure para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
2. Selecione **experimentar** para abrir Azure cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

* [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
* [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de início rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Solucionar erros comuns de implantação de Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
