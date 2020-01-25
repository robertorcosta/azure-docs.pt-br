---
title: Ingerir dados formatados JSON no Azure Data Explorer
description: Saiba mais sobre como ingerir dados formatados em JSON no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: ef5c7de782d833aad96516d3e5357a0ed575a781
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722870"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Ingerir dados de exemplo formatados em JSON no Azure Data Explorer

Este artigo mostra como ingerir dados formatados em JSON em um Azure Data Explorer Database. Você começará com exemplos simples de JSON bruto e mapeado, continuará com o JSON de várias linhas e, em seguida, resolverá esquemas JSON mais complexos contendo matrizes e dicionários. 

## <a name="prerequisites"></a>Pré-requisitos

[Um cluster de teste e um banco de dados](create-cluster-database-portal.md)

## <a name="the-json-format"></a>O formato JSON

O Azure Data Explorer dá suporte a dois formatos de arquivo JSON:
* `json`: JSON separado por linha. Cada linha nos dados de entrada tem exatamente um registro JSON.
* `multijson`: JSON com várias linhas. O analisador ignora os separadores de linha e lê um registro da posição anterior até o final de um JSON válido.

### <a name="ingest-and-map-json-formatted-data"></a>Ingerir e mapear dados formatados em JSON

A ingestão de dados formatados em JSON exige que você especifique o *formato* usando a [propriedade de ingestão](/azure/kusto/management/data-ingestion/index#ingestion-properties). A ingestão de dados JSON requer [mapeamento](/azure/kusto/management/mappings), que mapeia uma entrada de origem JSON para sua coluna de destino. Ao ingerir dados, use a propriedade de ingestão de `jsonMappingReference` predefinida ou especifique a propriedade de ingestão de `jsonMapping`. Este artigo usará a propriedade de ingestão `jsonMappingReference`, que é predefinida na tabela usada para ingestão. Nos exemplos a seguir, vamos começar ingerindo os registros JSON como dados brutos para uma única tabela de coluna. Em seguida, usaremos o mapeamento para ingerir cada propriedade para sua coluna mapeada. 

### <a name="simple-json-example"></a>Exemplo de JSON simples

O exemplo a seguir é um JSON simples, com uma estrutura plana. Os dados têm informações de temperatura e umidade, coletadas por vários dispositivos. Cada registro é marcado com uma ID e um carimbo de data/hora.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Registros JSON brutos de ingestão 

Neste exemplo, você ingerirá registros JSON como dados brutos para uma única tabela de coluna. A manipulação de dados, o uso de consultas e a política de atualização é feita depois que os dados são ingeridos.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Use a linguagem de consulta Kusto para ingerir dados em um formato JSON bruto.

1. Entre em [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Selecione **Adicionar cluster**.

1. Na caixa de diálogo **Adicionar cluster**, insira o URL do cluster no formulário e `https://<ClusterName>.<Region>.kusto.windows.net/`, selecione **Adicionar**.

1. Cole o comando a seguir e selecione **executar** para criar a tabela.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Essa consulta cria uma tabela com uma única coluna `Event` de um tipo de dados [dinâmico](/azure/kusto/query/scalar-data-types/dynamic) .

1. Crie o mapeamento JSON.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Esse comando cria um mapeamento e mapeia o caminho raiz JSON `$` para a coluna `Event`.

1. Ingerir dados na tabela `RawEvents`.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

    > [!NOTE]
    > Isso mostra os comandos de controle de `ingest` executados diretamente para o ponto de extremidade do mecanismo. Em cenários de produção, a ingestão é executada no serviço de Gerenciamento de Dados usando bibliotecas de cliente ou conexões de dados. Leia [dados de ingestão usando a biblioteca do azure data Explorer Python](/azure/data-explorer/python-ingest-data) e [ingerir dados usando o SDK do .NET Standard do Azure data Explorer](/azure/data-explorer/net-standard-ingest-data) para obter uma orientação sobre como ingerir dados com essas bibliotecas de cliente.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Use C# para ingerir dados no formato JSON bruto.

1. Crie a tabela `RawEvents`.

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
    Esse comando cria um mapeamento e mapeia o caminho raiz JSON `$` para a coluna `Event`.

1. Ingerir dados na tabela `RawEvents`.

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
> Os dados são agregados de acordo com a [política de envio em lote](/azure/kusto/concepts/batchingpolicy), resultando em uma latência de alguns minutos.

# <a name="pythontabpython"></a>[Python](#tab/python)

Use o Python para ingerir dados no formato JSON bruto.

1. Crie a tabela `RawEvents`.

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

1. Ingerir dados na tabela `RawEvents`.

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
    > Os dados são agregados de acordo com a [política de envio em lote](/azure/kusto/concepts/batchingpolicy), resultando em uma latência de alguns minutos.

---

## <a name="ingest-mapped-json-records"></a>Registros JSON mapeados de ingestão

Neste exemplo, você ingerirá dados de registros JSON. Cada propriedade JSON é mapeada para uma única coluna na tabela. 

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Crie uma nova tabela com um esquema semelhante aos dados de entrada JSON. Usaremos essa tabela para todos os exemplos e comandos de ingestão a seguir. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Crie o mapeamento JSON.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    Nesse mapeamento, conforme definido pelo esquema de tabela, as entradas de `timestamp` serão ingeridas para a coluna `Time` como `datetime` tipos de dados.

1. Ingerir dados na tabela `Events`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    O arquivo ' Simple. JSON ' tem alguns registros JSON separados por linha. O formato é `json`e o mapeamento usado no comando de ingestão é o `FlatEventMapping` que você criou.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Crie uma nova tabela com um esquema semelhante aos dados de entrada JSON. Usaremos essa tabela para todos os exemplos e comandos de ingestão a seguir. 

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

    Nesse mapeamento, conforme definido pelo esquema de tabela, as entradas de `timestamp` serão ingeridas para a coluna `Time` como `datetime` tipos de dados.    

1. Ingerir dados na tabela `Events`.

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

    O arquivo ' Simple. JSON ' tem alguns registros JSON separados por linha. O formato é `json`e o mapeamento usado no comando de ingestão é o `FlatEventMapping` que você criou.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Crie uma nova tabela com um esquema semelhante aos dados de entrada JSON. Usaremos essa tabela para todos os exemplos e comandos de ingestão a seguir. 

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

1. Ingerir dados na tabela `Events`.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    O arquivo ' Simple. JSON ' tem alguns registros JSON separados por linha. O formato é `json`e o mapeamento usado no comando de ingestão é o `FlatEventMapping` que você criou.    
---

## <a name="ingest-multi-lined-json-records"></a>Ingerir registros JSON com várias linhas

Neste exemplo, você ingerirá registros JSON com várias linhas. Cada propriedade JSON é mapeada para uma única coluna na tabela. O arquivo ' multilineed. JSON ' tem alguns registros JSON recuados. O formato `multijson` informa ao mecanismo para ler registros pela estrutura JSON.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Ingerir dados na tabela `Events`.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Ingerir dados na tabela `Events`.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Ingerir dados na tabela `Events`.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Ingerir registros JSON contendo matrizes

Tipos de dados de matriz são uma coleção ordenada de valores. A ingestão de uma matriz JSON é feita por uma [política de atualização](/azure/kusto/management/update-policy). O JSON é ingerido no estado em que se encontra em uma tabela intermediária. Uma política de atualização executa uma função predefinida na tabela de `RawEvents`, ingerindo os resultados para a tabela de destino. Iremos ingerir dados com a seguinte estrutura:

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

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Crie uma função `update policy` que expanda a coleção de `records` para que cada valor na coleção receba uma linha separada, usando o operador de `mv-expand`. Usaremos a tabela `RawEvents` como uma tabela de origem e `Events` como uma tabela de destino.

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

1. O esquema recebido pela função deve corresponder ao esquema da tabela de destino. Use `getschema` operador para examinar o esquema.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Adicione a política de atualização à tabela de destino. Essa política executará automaticamente a consulta em todos os dados recentemente incluídos na tabela `RawEvents` intermediária e ingerirá os resultados na tabela `Events`. Defina uma política de retenção zero para evitar a persistência da tabela intermediária.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Ingerir dados na tabela `RawEvents`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Examine os dados na tabela `Events`.

    ```Kusto
    Events
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Crie uma função de atualização que expanda a coleção de `records` para que cada valor na coleção receba uma linha separada, usando o operador de `mv-expand`. Usaremos a tabela `RawEvents` como uma tabela de origem e `Events` como uma tabela de destino.   

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

1. Adicione a política de atualização à tabela de destino. Essa política executará automaticamente a consulta em todos os dados recentemente incluídos na tabela `RawEvents` intermediária e ingerirá seus resultados na tabela `Events`. Defina uma política de retenção zero para evitar a persistência da tabela intermediária.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingerir dados na tabela `RawEvents`.

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
    
1. Examine os dados na tabela `Events`.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Crie uma função de atualização que expanda a coleção de `records` para que cada valor na coleção receba uma linha separada, usando o operador de `mv-expand`. Usaremos a tabela `RawEvents` como uma tabela de origem e `Events` como uma tabela de destino.   

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
    > O esquema recebido pela função deve corresponder ao esquema da tabela de destino.

1. Adicione a política de atualização à tabela de destino. Essa política executará automaticamente a consulta em todos os dados recentemente incluídos na tabela `RawEvents` intermediária e ingerirá seus resultados na tabela `Events`. Defina uma política de retenção zero para evitar a persistência da tabela intermediária.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados na tabela `RawEvents`.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Examine os dados na tabela `Events`.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Ingerir registros JSON contendo dicionários

O JSON estruturado do dicionário contém pares chave-valor. Os registros JSON passam pelo mapeamento de ingestão usando a expressão lógica no `JsonPath`. Você pode ingerir dados com a seguinte estrutura:

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

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Crie um mapeamento JSON.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Ingerir dados na tabela `Events`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

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

1. Ingerir dados na tabela `Events`.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Crie um mapeamento JSON.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados na tabela `Events`.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Próximos passos

* [Visão geral da ingestão de dados](ingest-data-overview.md)
* [Gravar consultas](write-queries.md)