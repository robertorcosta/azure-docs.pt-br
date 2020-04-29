---
title: Gerenciar Microsoft Azure Cosmos DB usando a CLI do Azure
description: Use a CLI do Azure para gerenciar sua conta, banco de dados e contêineres do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: mjbrown
ms.openlocfilehash: 3f86468bcafe3d7ce78827aba761bb4e1bf920fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273623"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Gerenciar Microsoft Azure Cosmos usando a CLI do Azure

O guia a seguir descreve os comandos comuns para automatizar o gerenciamento de contas, os bancos de dados e os contêineres do Azure Cosmos DB usando a CLI do Azure. As páginas de referência para todos os comandos da CLI do Azure Cosmos DB estão disponíveis na [referência de CLI do Azure](https://docs.microsoft.com/cli/azure/cosmosdb). Você também pode encontrar mais exemplos em [amostras de CLI do Azure para o Azure Cosmos DB](cli-samples.md), incluindo como criar e gerenciar contas, bancos de dados e contêineres do Cosmos DB para MongoDB, Gremlin, Cassandra e API de Tabela.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Criar uma conta de Azure Cosmos DB com a API do SQL, consistência de sessão nas regiões oeste dos EUA 2 e leste dos EUA 2:

> [!IMPORTANT]
> O nome da conta do Azure Cosmos deve ser minúsculo e ter menos de 31 caracteres.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 31 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

## <a name="add-or-remove-regions"></a>Adicionar ou remover regiões

Crie uma conta do Azure cosmos com duas regiões, adicione uma região e remova uma região.

> [!NOTE]
> Não é possível adicionar ou remover `locations` de regiões simultaneamente nem alterar outras propriedades de uma conta do Azure Cosmos. A modificação de regiões precisa ser executada como uma operação separada de qualquer outra alteração no recurso da conta.
> [!NOTE]
> Este comando permite adicionar e remover regiões, mas não permite modificar as prioridades de failover nem disparar um failover manual. Consulte [definir prioridade de failover](#set-failover-priority) e [disparar failover manual](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount' # must be lower case and <31 characters

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

## <a name="enable-multiple-write-regions"></a>Habilitar várias regiões de gravação

Habilitar vários mestres para uma conta do cosmos

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

## <a name="set-failover-priority"></a>Definir prioridade de failover

Definir a prioridade de failover para uma conta do Azure Cosmos configurada para failover automático

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2'=0 'South Central US'=1 'East US 2'=2
```

## <a name="enable-automatic-failover"></a> Habilitar failover automático

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

## <a name="trigger-manual-failover"></a>Disparar failover manual

> [!CAUTION]
> A alteração da região com prioridade = 0 disparará um failover manual para uma conta do Azure Cosmos. Nenhuma outra alteração de prioridade disparará um failover.

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2'=0 'South Central US'=1 'West US 2'=2
```

## <a name="list-all-account-keys"></a><a id="list-account-keys"></a>Listar todas as chaves de conta

Obter todas as chaves para uma conta do cosmos.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

## <a name="list-read-only-account-keys"></a>Listar chaves de conta somente leitura

Obter chaves somente leitura para uma conta do cosmos.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

## <a name="list-connection-strings"></a>Cadeias de caracteres de conexão de lista

Obtenha as cadeias de conexão para uma conta do cosmos.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="regenerate-account-key"></a>Regenerar a chave de conta

Regenerar uma nova chave para uma conta do cosmos.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="create-a-database"></a>Criar um banco de dados

Crie um banco de dados Cosmos.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

## <a name="create-a-database-with-shared-throughput"></a>Criar um banco de dados com produtividade compartilhada

Crie um banco de dados cosmos com taxa de transferência compartilhada.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

## <a name="change-the-throughput-of-a-database"></a>Alterar a taxa de transferência de um banco de dados

Aumente a taxa de transferência de um banco de dados Cosmos de 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

## <a name="create-a-container"></a>Criar um contêiner

Crie um contêiner cosmos com política de índice padrão, chave de partição e RU/s de 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

## <a name="create-a-container-with-ttl"></a>Criar um contêiner com TTL

Crie um contêiner cosmos com TTL habilitado.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

## <a name="create-a-container-with-a-custom-index-policy"></a>Criar um contêiner com uma política de índice personalizada

Crie um contêiner cosmos com uma política de índice personalizada, um índice espacial, um índice composto, uma chave de partição e RU/s de 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

## <a name="change-the-throughput-of-a-container"></a>Alterar o rendimento de um contêiner

Aumente a taxa de transferência de um contêiner Cosmos de 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure, consulte:

- [Instalar a CLI do Azure.](/cli/azure/install-azure-cli)
- [Referência de CLI do Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Amostras adicionais do CLI do Azure para o Azure Cosmos DB](cli-samples.md)
