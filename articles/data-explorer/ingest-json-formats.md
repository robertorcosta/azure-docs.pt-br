---
title: Inseria os dados formatados json no Azure Data Explorer
description: Saiba como ingerir dados formatados JSON no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d293b76e004d693813a074cb8551a86cb3c0bec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772341"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Inserique dados de amostra formatados json no Azure Data Explorer

Este artigo mostra como ingerir dados formatados JSON em um banco de dados do Azure Data Explorer. Você começará com exemplos simples de JSON cru e mapeado, continuará a JSON multi-alinhado e, em seguida, enfrentará esquemas JSON mais complexos contendo matrizes e dicionários.  Os exemplos detalham o processo de ingestão de dados formatados json usando a linguagem de consulta Kusto (KQL), C#ou Python. Os comandos de `ingest` controle de linguagem de consulta kusto são executados diretamente no ponto final do mecanismo. Em cenários de produção, a ingestão é executada no serviço de Gerenciamento de Dados usando bibliotecas de clientes ou conexões de dados. Leia [os dados do Azure Data Explorer Python](/azure/data-explorer/python-ingest-data) e [inserimos dados usando o Azure Data Explorer .NET Standard SDK](/azure/data-explorer/net-standard-ingest-data) para um passo a passo sobre a ingestão de dados com essas bibliotecas clientes.

## <a name="prerequisites"></a>Pré-requisitos

[Um cluster de teste e banco de dados](create-cluster-database-portal.md)

## <a name="the-json-format"></a>O formato JSON

O Azure Data Explorer suporta dois formatos de arquivo JSON:
* `json`: Linha separada JSON. Cada linha nos dados de entrada tem exatamente um registro JSON.
* `multijson`: JSON multi-alinhado. O analisador ignora os separadores de linha e lê um registro da posição anterior até o final de um JSON válido.

### <a name="ingest-and-map-json-formatted-data"></a>Ingere e mapeie dados formatados json

A ingestão de dados formatados JSON requer que você especifique o *formato* usando [a propriedade de ingestão](/azure/kusto/management/data-ingestion/index#ingestion-properties). A ingestão de dados JSON requer [mapeamento,](/azure/kusto/management/mappings)que mapeia uma entrada de origem JSON em sua coluna de destino. Ao ingerar dados, use a `jsonMappingReference` propriedade de ingestão `jsonMapping`pré-definida ou especifique a propriedade de ingestão. Este artigo utilizará a `jsonMappingReference` propriedade de ingestão, que está pré-definida na tabela utilizada para a ingestão. Nos exemplos abaixo, começaremos ingerindo registros JSON como dados brutos para uma única tabela de colunas. Em seguida, usaremos o mapeamento para ingerir cada propriedade em sua coluna mapeada. 

### <a name="simple-json-example"></a>Exemplo JSON simples

O exemplo a seguir é um JSON simples, com uma estrutura plana. Os dados têm informações de temperatura e umidade, coletadas por vários dispositivos. Cada registro é marcado com um ID e carimbo de data e hora.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Ingest raw JSON records 

Neste exemplo, você ingere registros JSON como dados brutos para uma única tabela de colunas. A manipulação de dados, usando consultas e a política de atualização são feitas após a ingestão dos dados.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Use a linguagem de consulta Kusto para ingerir dados em um formato JSON bruto.

1. Faça login [https://dataexplorer.azure.com](https://dataexplorer.azure.com)em .

1. Selecione **Adicionar cluster**.

1. Na caixa de diálogo **Adicionar cluster**, insira o URL do cluster no formulário e `https://<ClusterName>.<Region>.kusto.windows.net/`, selecione **Adicionar**.

1. Cole no comando a seguir e **selecione Executar** para criar a tabela.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Esta consulta cria uma tabela `Event` com uma única coluna de um tipo de dados [dinâmico.](/azure/kusto/query/scalar-data-types/dynamic)

1. Crie o mapeamento JSON.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Esse comando cria um mapeamento e mapeia o caminho `$` raiz JSON para a `Event` coluna.

1. Ingerir dados `RawEvents` na tabela.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C #](#tab/c-sharp)

Use C# para ingerir dados no formato JSON bruto.

1. Crie `RawEvents` a mesa.

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Crie o mapeamento JSON.
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    Esse comando cria um mapeamento e mapeia o caminho `$` raiz JSON para a `Event` coluna.

1. Ingerir dados `RawEvents` na tabela.

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> Os dados são agregados de acordo com a [política de loteamento,](/azure/kusto/concepts/batchingpolicy)resultando em uma latência de poucos minutos.

# <a name="python"></a>[Python](#tab/python)

Use Python para ingerir dados no formato JSON bruto.

1. Crie `RawEvents` a mesa.

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Crie o mapeamento JSON.

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados `RawEvents` na tabela.

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Os dados são agregados de acordo com a [política de loteamento,](/azure/kusto/concepts/batchingpolicy)resultando em uma latência de poucos minutos.

---

## <a name="ingest-mapped-json-records"></a>Ingest mapeou registros JSON

Neste exemplo, você ingere dados de registros JSON. Cada propriedade JSON é mapeada para uma única coluna na tabela. 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Crie uma nova tabela, com um esquema semelhante aos dados de entrada JSON. Usaremos esta tabela para todos os seguintes exemplos e comandos de ingestão. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Crie o mapeamento JSON.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    Neste mapeamento, conforme definido pelo esquema da `timestamp` tabela, as entradas serão `Time` `datetime` ingeridas na coluna como tipos de dados.

1. Ingerir dados `Events` na tabela.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    O arquivo 'simple.json' tem alguns registros JSON separados por linha. O formato `json`é , e o mapeamento usado `FlatEventMapping` no comando ingest é o que você criou.

# <a name="c"></a>[C #](#tab/c-sharp)

1. Crie uma nova tabela, com um esquema semelhante aos dados de entrada JSON. Usaremos esta tabela para todos os seguintes exemplos e comandos de ingestão. 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Crie o mapeamento JSON.

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    Neste mapeamento, conforme definido pelo esquema da `timestamp` tabela, as entradas serão `Time` `datetime` ingeridas na coluna como tipos de dados.    

1. Ingerir dados `Events` na tabela.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    O arquivo 'simple.json' tem alguns registros JSON separados por linha. O formato `json`é , e o mapeamento usado `FlatEventMapping` no comando ingest é o que você criou.

# <a name="python"></a>[Python](#tab/python)

1. Crie uma nova tabela, com um esquema semelhante aos dados de entrada JSON. Usaremos esta tabela para todos os seguintes exemplos e comandos de ingestão. 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Crie o mapeamento JSON.

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados `Events` na tabela.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    O arquivo 'simple.json' tem alguns registros JSON separados de linha. O formato `json`é , e o mapeamento usado `FlatEventMapping` no comando ingest é o que você criou.    
---

## <a name="ingest-multi-lined-json-records"></a>Ingerir registros JSON multi-alinhados

Neste exemplo, você ingere registros JSON multi-alinhados. Cada propriedade JSON é mapeada para uma única coluna na tabela. O arquivo 'multilined.json' tem alguns registros JSON recuados. O `multijson` formato diz ao motor para ler registros pela estrutura JSON.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Ingerir dados `Events` na tabela.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C #](#tab/c-sharp)

Ingerir dados `Events` na tabela.

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="python"></a>[Python](#tab/python)

Ingerir dados `Events` na tabela.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Ingere registros JSON contendo matrizes

Tipos de dados de matriz são uma coleção ordenada de valores. A ingestão de uma matriz JSON é feita por uma [política de atualização](/azure/kusto/management/update-policy). O JSON é ingerido como-é para uma tabela intermediária. Uma diretiva de atualização executa `RawEvents` uma função pré-definida na tabela, reingerindo os resultados para a tabela de destino. Vamos ingerr dados com a seguinte estrutura:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Crie `update policy` uma função que expanda a coleção de `records` modo que cada valor `mv-expand` na coleção receba uma linha separada, utilizando o operador. Usaremos a `RawEvents` mesa como `Events` uma tabela de origem e como uma tabela alvo.

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. O esquema recebido pela função deve corresponder ao esquema da tabela de destino. Use `getschema` o operador para revisar o esquema.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Adicione a política de atualização à tabela de destino. Esta diretiva executará automaticamente a consulta sobre quaisquer dados `RawEvents` recém-ingeridos na `Events` tabela intermediária e ingerirá os resultados na tabela. Defina uma política de retenção zero para evitar a persistência da tabela intermediária.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Ingerir dados `RawEvents` na tabela.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Revise os `Events` dados na tabela.

    ```Kusto
    Events
    ```

# <a name="c"></a>[C #](#tab/c-sharp)

1. Crie uma função de atualização `records` que expanda a coleção de modo que `mv-expand` cada valor na coleção receba uma linha separada, usando o operador. Usaremos a `RawEvents` mesa como `Events` uma tabela de origem e como uma tabela alvo.   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > O esquema recebido pela função deve corresponder ao esquema da tabela de destino.

1. Adicione a política de atualização à tabela de destino. Esta diretiva executará automaticamente a consulta sobre quaisquer dados `RawEvents` recém-ingeridos na `Events` tabela intermediária e ingerirá seus resultados na tabela. Defina uma política de retenção zero para evitar a persistência da tabela intermediária.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingerir dados `RawEvents` na tabela.

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Revise os `Events` dados na tabela.

# <a name="python"></a>[Python](#tab/python)

1. Crie uma função de atualização `records` que expanda a coleção de modo que `mv-expand` cada valor na coleção receba uma linha separada, usando o operador. Usaremos a `RawEvents` mesa como `Events` uma tabela de origem e como uma tabela alvo.   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > O esquema recebido pela função tem que corresponder ao esquema da tabela de destino.

1. Adicione a política de atualização à tabela de destino. Esta diretiva executará automaticamente a consulta sobre quaisquer dados `RawEvents` recém-ingeridos na `Events` tabela intermediária e ingerirá seus resultados na tabela. Defina uma política de retenção zero para evitar a persistência da tabela intermediária.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados `RawEvents` na tabela.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Revise os `Events` dados na tabela.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Ingest Registros JSON contendo dicionários

O JSON estruturado pelo dicionário contém pares de valor-chave. Os registros de Json passam por `JsonPath`mapeamento de ingestão usando expressão lógica no . Você pode ingerr dados com a seguinte estrutura:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Crie um mapeamento JSON.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Ingerir dados `Events` na tabela.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C #](#tab/c-sharp)

1. Crie um mapeamento JSON.

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingerir dados `Events` na tabela.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="python"></a>[Python](#tab/python)

1. Crie um mapeamento JSON.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados `Events` na tabela.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de ingestão de dados](ingest-data-overview.md)
* [Gravar consultas](write-queries.md)