---
title: Configurar e usar o Link do Azure Synapse para Azure Cosmos DB (versão prévia)
description: Saiba como habilitar o link do Synapse para contas do Azure Cosmos, criar um contêiner com o repositório analítico habilitado, conectar o banco de dados do Azure Cosmos ao espaço de trabalho do Synapse e executar consultas.
author: SriChintala
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: d2a10d064bed3e2e2e798d16ce72ccf55c965f8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262031"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Configurar e usar o Link do Azure Synapse para Azure Cosmos DB (versão prévia)

O Link do Azure Synapse para Azure Cosmos DB é uma funcionalidade HTAP (transacional e de processamento analítico) híbrida e nativa de nuvem que permite executar análises quase em tempo real sobre dados operacionais no Azure Cosmos DB. O Link do Synapse cria uma integração perfeita entre o Azure Cosmos DB e o Azure Synapse Analytics.


> [!IMPORTANT]
> Para usar o Link do Azure Synapse, certifique-se de provisionar sua conta do Azure Cosmos e do espaço de trabalho do Azure Synapse Analytics em uma das regiões com suporte acima. Para obter a lista de regiões com suporte, consulte [atualizações de serviço do Azure](https://azure.microsoft.com/updates/). 

Use as etapas a seguir para executar consultas analíticas com o Link do Azure Synapse para Azure Cosmos DB:

* [Habilitar o Link do Synapse para suas contas do Azure Cosmos](#enable-synapse-link)
* [Criar um contêiner do Azure Cosmos habilitado para repositório analítico](#create-analytical-ttl)
* [Conectar o banco de dados Azure Cosmos a um espaço de trabalho do Synapse](#connect-to-cosmos-database)
* [Consultar o repositório analítico usando o Synapse Spark](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Habilitar o Link do Azure Synapse para contas do Azure Cosmos

### <a name="azure-portal"></a>Portal do Azure

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure](create-sql-api-dotnet.md#create-account) ou selecione uma conta existente do Azure Cosmos.

1. Navegue até a conta do Azure Cosmos e abra o painel **Recursos**.

1. Selecione o **Link do Synapse** na lista de recursos.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Localizar a versão prévia do recurso do Link do Synapse":::

1. Em seguida, ele solicita que você habilite o Link do Synapse em sua conta. Selecione Habilitar.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Habilitar o recurso Link do Synapse":::

1. Sua conta agora está habilitada para usar o Link do Synapse. Em seguida, consulte como criar contêineres habilitados para repositório analítico para iniciar automaticamente a replicação dos dados operacionais do repositório transacional no repositório analítico.

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

O [modelo do Azure Resource Manager](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) cria uma conta do Azure Cosmos habilitada para Synapse Link para a API do SQL. Este modelo cria uma conta de API do Core (SQL) em uma região com um contêiner configurado com o TTL analítico habilitado e uma opção de usar taxa de transferência de dimensionamento manual ou automático. Para implantar esse modelo, clique em **Implantar no Azure** na página do arquivo Leia-me.

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a>Criar um contêiner do Azure Cosmos com repositório analítico

Você pode ativar o repositório analítico em um contêiner Azure Cosmos ao criar o contêiner. Você pode usar o portal do Azure ou configurar a propriedade `analyticalTTL` durante a criação do contêiner usando os SDKs do Azure Cosmos DB.

> [!NOTE]
> No momento, você pode habilitar o repositório analítico para **novos** contêineres (em contas novas e existentes).

### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/) ou no [Azure Cosmos Explorer](https://cosmos.azure.com/).

1. Navegue até a conta do Azure Cosmos e abra a guia do **Data Explorer**.

1. Selecione **Novo Contêiner** e insira um nome para o banco de dados, o contêiner, a chave de partição e os detalhes da taxa de transferência. Ative a opção **Repositório analítico**. Depois de habilitar o repositório analítico, ele cria um contêiner com `AnalyicalTTL` propriedade definida como o valor padrão de -1 (retenção infinita). Esse repositório analítico retém todas as versões históricas dos registros.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Ativar repositório analítico para o contêiner do Azure Cosmos":::

1. Se você não tiver habilitado o Link do Synapse anteriormente nessa conta, ele solicitará que você faça isso porque é um pré-requisito para criar um contêiner habilitado para repositório analítico. Se solicitado, selecione **Habilitar Link do Synapse**.

1. Selecione **OK** para criar um contêiner do Azure Cosmos habilitado para repositório analítico.

### <a name="net-sdk"></a>SDK .NET

O código a seguir cria um contêiner com o repositório analítico usando o SDK do .NET. Defina a propriedade do TTL analítico com o valor necessário. Para obter a lista de valores permitidos, consulte o artigo sobre [valores aceitos no TTL analítico](analytical-store-introduction.md#analytical-ttl):

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>SDK do Java v4

O código a seguir cria um contêiner com o repositório analítico usando o SDK do Java V4. Defina a propriedade `AnalyticalStoreTimeToLiveInSeconds` com o valor necessário:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v3-sdk"></a>SDK do Python V3

O código a seguir cria um contêiner com o repositório analítico usando o SDK do Python:

```python
import azure.cosmos.cosmos_client as cosmos_client
def create_collection_if_not_exists(cosmosEndpoint, cosmosKey, databaseName, collectionName):
    client = cosmos_client.CosmosClient(url_connection=cosmosEndpoint, auth={'masterKey': cosmosKey})

db = client.QueryDatabases("select * from c where c.id = '" + databaseName + "'").fetch_next_block()[0]
options = {
    'offerThroughput': 1000
}

container_definition = {
    'id': collectionName,
    "partitionKey": {  
        "paths": [  
        "/id"  
        ],  
        "kind": "Hash" 
    },
    "indexingPolicy": {  
    "indexingMode": "consistent",  
    "automatic": True,  
    "includedPaths": [],  
    "excludedPaths": [{
        "path": "/*"
    }]  
    },
    "defaultTtl": -1,
    "analyticalStorageTtl": -1
}

container = client.CreateContainer(db['_self'], container_definition, options)
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Atualizar a vida útil do repositório analítico

Depois que o repositório analítico é habilitado com um determinado valor de TTL, é possível atualizá-lo posteriormente com um valor válido diferente. Você pode atualizar o valor usando o portal do Azure ou SDKs. Para obter informações sobre as várias opções de configuração do TTL analítico, consulte o artigo sobre [valores aceitos no TTL analítico](analytical-store-introduction.md#analytical-ttl).

#### <a name="azure-portal"></a>Portal do Azure

Se você criou um contêiner de repositório analítico habilitado por meio do portal do Azure, ele contém um TTL analítico padrão de -1. Use as etapas a seguir para atualizar este valor:

1. Entre no [portal do Azure](https://portal.azure.com/) ou no [Azure Cosmos Explorer](https://cosmos.azure.com/).

1. Navegue até a conta do Azure Cosmos e abra a guia do **Data Explorer**.

1. Selecione um contêiner existente que tenha o repositório analítico habilitado. Expanda-o e modifique os seguintes valores:

  * Abra a janela **Escala e Configurações**.
  * Em **Configuração** localize **Vida útil do armazenamento analítico**.
  * Selecione **Ativado (não há padrão)** ou selecione **Ativado** e defina um valor de TTL
  * Clique em **Salvar** para salvar as alterações.

#### <a name="net-sdk"></a>SDK .NET

O código a seguir mostra como atualizar o TTL para o repositório analítico usando o SDK do .NET:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>SDK do Java v4

O código a seguir mostra como atualizar o TTL para o repositório analítico usando o SDK do Java V4:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Conectar-se a um espaço de trabalho do Synapse

Use as instruções em [Conectar-se ao Link do Azure Synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) sobre como acessar um banco de dados Azure Cosmos DB do Azure Synapse Analytics Studio com o Link do Azure Synapse.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a> Consultar usando o Synapse Spark

Use as instruções no artigo [Consultar o repositório analítico do Azure Cosmos DB](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) sobre como consultar com o Synapse Spark. Esse artigo fornece alguns exemplos sobre como você pode interagir com o armazenamento analítico de gestos do Synapse. Esses gestos ficam visíveis quando você clica com o botão direito do mouse em um contêiner. Com gestos, você pode gerar código rapidamente e ajustá-lo às suas necessidades. Eles também são perfeitos para descobrir dados com um único clique.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Introdução ao Link do Azure Synapse - exemplos

Você pode encontrar exemplos para começar a usar o link Synapse do Azure no [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Eles demostram soluções ponta a ponta com cenários de varejo e IoT.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte a seguinte documentação:

* [Link do Azure Synapse para Azure Cosmos DB.](synapse-link.md)

* [Visão geral do repositório analítico do Azure Cosmos DB.](analytical-store-introduction.md)

* [Perguntas frequentes sobre o Link do Synapse para Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Apache Spark no Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [SQL sem servidor/sob demanda no Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
