---
title: Copiar e transformar dados no Azure Cosmos DB (API SQL)
description: Aprenda a copiar dados para e para o Azure Cosmos DB (SQL API) e transformar dados no Azure Cosmos DB (SQL API) usando a Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: f0aa70333454b327a0ca76beef2985062ce56715
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415374"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiar e transformar dados no Azure Cosmos DB (SQL API) usando a Fábrica de Dados Do Azure

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versão atual](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Azure Cosmos DB (API SQL) e usar o Fluxo de Dados para transformar dados no Azure Cosmos DB (API SQL). Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).



>[!NOTE]
>Este conector só suporta a API Cosmos DB SQL. Para a API do MongoDB, confira o [conector para API do Azure Cosmos DB para MongoDB](connector-azure-cosmos-db-mongodb-api.md). Não há suporte para outros tipos de API.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Azure Cosmos DB (SQL API) é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Mapeamento do fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Para a atividade copy, este conector Azure Cosmos DB (API SQL) suporta:

- Copiar dados para e da [API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) do Azure Cosmos DB.
- Gravar no Azure Cosmos DB como **insert** ou **upsert**.
- Importar e exportar documentos JSON no estado em que se encontram, ou copiar dados de ou para um conjunto de dados tabular. Exemplos incluem um Banco de Dados SQL e um arquivo CSV. Para copiar documentos como está para ou a partir de arquivos JSON ou para ou de outra coleção azure Cosmos DB, consulte [Importar e exportar documentos JSON](#import-and-export-json-documents).

O Data Factory integra-se com a [biblioteca de executor em massa do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para fornecer o melhor desempenho quando você escreve para o Azure Cosmos DB.

> [!TIP]
> O [vídeo de Migração de Dados](https://youtu.be/5-SRNiC_qOU) orienta você pelas etapas de copiar dados do Armazenamento de Blobs do Azure para o Azure Cosmos DB. O vídeo também descreve considerações de ajuste de desempenho para a ingestão de dados para o Azure Cosmos DB em geral.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas para o Azure Cosmos DB (API do SQL).

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Cosmos DB (API do SQL):

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **type** deve ser definida como **CosmosDb**. | Sim |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados do Azure Cosmos DB.<br />**Observação**: você deve especificar informações de banco de dados na cadeia de conexão conforme mostrado nos exemplos a seguir. <br/> Você também pode colocar a chave de conta no Azure Key Vault e efetuar pull da configuração `accountKey` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você poderá usar o Azure Integration Runtime ou um tempo de execução da integração auto-hospedada (se o armazenamento de dados estiver localizado em uma rede privada). Se essa propriedade não for especificada, o Azure Integration Runtime padrão será usado. |Não |

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a chave da conta no Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md).

As seguintes propriedades são suportadas para o conjunto de dados Azure Cosmos DB (SQL API): 

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do **tipo** do conjunto de dados deve ser definida como **CosmosDbSqlApiCollection**. |Sim |
| collectionName |O nome da coleção de documentos do Azure Cosmos DB. |Sim |

Se você usar o conjunto de dados do tipo "DocumentDbCollection", ele ainda é suportado como é para compatibilidade retrógrada para a atividade copiar e procurar, ele não é suportado para fluxo de dados. Você está sugerido a usar o novo modelo daqui para frente.

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista das propriedades compatíveis com fonte e coletor do Azure Cosmos DB (API do SQL). Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API do SQL) como origem

Para copiar dados do Azure Cosmos DB (API do SQL), defina o tipo **source** na Atividade de Cópia como **DocumentDbCollectionSource**. 

As seguintes propriedades são suportadas na seção Origem atividade **de** cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** da fonte de atividade de cópia deve ser definida como **CosmosDbSqlApiSource**. |Sim |
| Consulta |Especifique a consulta do Azure Cosmos DB para ler dados.<br/><br/>Exemplo:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificada, esta instrução SQL será executada: `select <columns defined in structure> from mycollection` |
| regiões preferenciais | A lista preferida de regiões para se conectar ao recuperar dados do Cosmos DB. | Não |
| pageSize | O número de documentos por página do resultado da consulta. O padrão é "-1", o que significa que usa o tamanho da página dinâmica do lado do serviço até 1000. | Não |

Se você usar a fonte do tipo "DocumentDbCollectionSource", ela ainda será suportada para compatibilidade retrógrada. Você é sugerido a usar o novo modelo daqui para frente que fornece recursos mais ricos para copiar dados do Cosmos DB.

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ao copiar dados do Cosmos DB, a menos que você queira [exportar documentos JSON como está,](#import-and-export-json-documents)a melhor prática é especificar o mapeamento na atividade de cópia. Data Factory honra o mapeamento especificado na atividade - se uma linha não contiver um valor para uma coluna, um valor nulo é fornecido para o valor da coluna. Se você não especificar um mapeamento, a Data Factory inferirá o esquema usando a primeira linha nos dados. Se a primeira linha não contiver o esquema completo, algumas colunas estarão faltando no resultado da operação de atividade.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API do SQL) como coletor

Para copiar dados para o Azure Cosmos DB (API do SQL), defina o tipo **sink** na Atividade de Cópia como **DocumentDbCollectionSink**. 

As seguintes propriedades são suportadas na seção Origem atividade **de** cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A **type** propriedade tipo do dissipador Copy Activity deve ser definida como **CosmosDbSqlApiSink**. |Sim |
| writeBehavior |Descreve como gravar dados no Azure Cosmos DB. Valores permitidos são **insert** e **upsert**.<br/><br/>O comportamento de **upsert** é substituir o documento se um documento com a mesma ID já existir; caso contrário, inserir o documento.<br /><br />**Observação**: o Data Factory gerará automaticamente uma ID para um documento se uma ID não for especificada no documento original ou pelo mapeamento de coluna. Isso significa que, para **upsert** funcionar conforme esperado, o documento deve ter uma ID. |Não<br />(o padrão é **insert**) |
| writeBatchSize | O Data Factory usa a [biblioteca de executor em massa do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para gravar dados no Azure Cosmos DB. A propriedade **writeBatchSize** controla o tamanho dos documentos que o ADF fornece à biblioteca. Você pode tentar aumentar o valor de **writeBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do documento for grande - veja as dicas abaixo. |Não<br />(o padrão é **10.000**) |
| desativaçãoMetricsCollection | A Data Factory coleta métricas como Cosmos DB RUs para otimização e recomendações de desempenho de cópia. Se você está preocupado com `true` esse comportamento, especifique para desligá-lo. | Não (o padrão é `false`) |

>[!TIP]
>Para importar documentos JSON como está, consulte a seção [Importar ou exportar documentos JSON;](#import-and-export-json-documents) para copiar a partir de dados em forma de tabular, consulte [Migrar do banco de dados relacional para o Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>O Cosmos DB limita o tamanho da solicitação única para 2MB. A fórmula é Tamanho da Solicitação = Tamanho Único do Documento * Tamanho do Lote de Gravação. Se você digitar o erro dizendo **"O tamanho da solicitação é muito grande"**, **reduza o `writeBatchSize`valor** na configuração do coletor de cópia.

Se você usar a fonte do tipo "DocumentDbCollectionSink", ela ainda será suportada para compatibilidade retrógrada. Você é sugerido a usar o novo modelo daqui para frente que fornece recursos mais ricos para copiar dados do Cosmos DB.

**Exemplo**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados do Azure Cosmos DB para pia tabular ou invertida, consulte o [mapeamento do esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no mapeamento do fluxo de dados, você pode ler e gravar em coleções no Cosmos DB. Para obter mais informações, consulte a [transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) no mapeamento dos fluxos de dados.

### <a name="source-transformation"></a>Transformação de origem

As configurações específicas do Azure Cosmos DB estão disponíveis na guia **Opções** de Origem da transformação de origem. 

**Inclua colunas do sistema:** Se for ```id``` ```_ts```verdade, e outras colunas do sistema serão incluídas em seus metadados de fluxo de dados do CosmosDB. Ao atualizar as coleções, é importante incluí-lo para que você possa pegar o id de linha existente.

**Tamanho da página:** O número de documentos por página do resultado da consulta. O padrão é "-1" que usa a página dinâmica do serviço até 1000.

**Throughput:** Defina um valor opcional para o número de RUs que você gostaria de aplicar à sua coleção CosmosDB para cada execução desse fluxo de dados durante a operação de leitura. O mínimo é 400.

**Regiões preferenciais:** Escolha as regiões de leitura preferidas para este processo.

#### <a name="json-settings"></a>Configurações de JSON

**Documento único:** Selecione esta opção se o ADF for tratar todo o arquivo como um único doc JSON.

**Nomes de colunas não citados:** Selecione esta opção se os nomes da coluna no JSON não forem citados.

**Tem comentários:** Use esta seleção se seus documentos JSON tiverem comentários nos dados.

**Único citado:** Isso deve ser selecionado se as colunas e valores do documento forem citados com aspas simples.

**Barra invertida escapou:** Se usar barras traseiras para escapar de caracteres em seu JSON, escolha esta opção.

### <a name="sink-transformation"></a>Transformação da pia

As configurações específicas do Azure Cosmos DB estão disponíveis na guia **Configurações** da transformação do dissipador.

**Método de atualização:** Determina quais operações são permitidas no destino do seu banco de dados. O padrão é permitir apenas inserções. Para atualizar, upsert ou excluir linhas, uma transformação de alter-row é necessária para marcar linhas para essas ações. Para atualizações, upserts e exclusões, uma coluna de chave ou colunas devem ser definidas para determinar qual linha alterar.

**Ação de cobrança:** Determina se deve recriar a coleção de destino antes de escrever.
* Nenhuma: Nenhuma ação será feita para a coleta.
* Recriar: A coleção será descartada e recriada

**Tamanho do lote**: Controla quantas linhas estão sendo escritas em cada balde. Tamanhos de lote maiores melhoram a otimização de compactação e memória, mas correm o risco de exceções de memória ao cachear dados.

**Chave de partição:** Digite uma string que representa a chave de partição para sua coleção. Exemplo: ```/movies/title```

**Throughput:** Defina um valor opcional para o número de RUs que você gostaria de aplicar à sua coleção CosmosDB para cada execução desse fluxo de dados. O mínimo é 400.

**Escrever orçamento de throughput:** Um inteiro que representa o número de RUs que você deseja alocar para o trabalho de ingestão em massa Spark. Este número está fora do rendimento total alocado para a coleção.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importação e exportação de documentos JSON

Use esse conector do Azure Cosmos DB (API do SQL) para facilmente:

* Copiar documentos entre duas coleções do Azure Cosmos DB no estado em que se encontram.
* Importar documentos JSON de várias fontes para o Azure Cosmos DB, incluindo o armazenamento de BLOBs do Azure, Azure Data Lake Storage e outros repositórios baseados em arquivo compatíveis com o Azure Data Factory.
* Exportar documentos JSON de uma coleção do Azure Cosmos DB para vários repositórios baseados em arquivo.

Para obter uma cópia independente de esquema:

* Quando você usa a ferramenta Copiar Dados, selecione a opção **Exportar no estado em que se encontram para arquivos JSON ou uma coleção do Cosmos DB**.
* Ao usar a autoria da atividade, escolha o formato JSON com a loja de arquivos correspondente para origem ou pia.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrar do banco de dados relacional para o Cosmos DB

Ao migrar de um banco de dados relacional, por exemplo, SQL Server para Azure Cosmos DB, a atividade de cópia pode facilmente mapear dados tabulares da fonte para achatar documentos JSON no Cosmos DB. Em alguns casos, você pode querer redesenhar o modelo de dados para otimizá-lo para os casos de uso do NoSQL de acordo com a [modelagem de dados no Azure Cosmos DB](../cosmos-db/modeling-data.md), por exemplo, para desnormalizar os dados incorporando todos os subitens relacionados em um documento JSON. Para esse caso, consulte [este artigo](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) com um passo a passo sobre como alcançá-lo usando a atividade de cópia da Fábrica de Dados do Azure.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados compatíveis com a Atividade de Cópia como fontes e coletores no Azure Data Factory, confira [armazenamentos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
