---
title: ALTER EXTERNAL STREAM (Transact-SQL) – SQL do Azure no Edge (visualização)
description: Saiba mais sobre a instrução CREATE EXTERNAL STREAM no SQL do Azure no Edge (visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d4ad446d43c90eee1c48ee2ba94585242805fa7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595395"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

O objeto EXTERNAL STREAM tem uma finalidade dupla de entrada e saída. Ele pode ser usado como uma entrada para consultar dados de streaming de serviços de ingestão de eventos, como os hubs de eventos e IoT do Azure, ou usado como uma saída para especificar onde e como armazenar os resultados de uma consulta de streaming.

Um EXTERNAL STREAM também pode ser especificado e criado como uma saída e uma entrada para serviços como o hub de eventos ou o armazenamento de blobs. Isso é para cenários de encadeamento em que uma consulta de streaming está persistindo os resultados para o stream externo como saída e outra consulta de streaming lendo do mesmo fluxo externo como entrada. 


## <a name="syntax"></a>Sintaxe

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Argumentos


- [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION**: Especifica o nome para os dados reais ou o local na fonte de dados. No caso de um objeto de fluxo Kafka ou Hub do Edge, o local especifica o nome do tópico Kafka ou Hub do Edge do qual ler ou no qual gravar.
- **INPUT_OPTIONS**: Especificar opções como pares chave-valor para serviços como hubs IOT e de eventos que são entradas para consultas de streaming
    - CONSUMER_GROUP: Os hubs IoT e de eventos limitam a 5 o número de leitores em um grupo de consumidores. Deixar esse campo vazio usará o grupo de consumidores '$Default'.
      - Aplica-se aos hubs de eventos e IoT
    - TIME_POLICY: Descreve se deve-se descartar eventos ou ajustar a hora do evento quando eventos atrasados ou fora de ordem passam o valor de tolerância.
      - Aplica-se aos hubs de eventos e IoT
    - LATE_EVENT_TOLERANCE: O atraso de tempo máximo aceitável. O atraso representa a diferença entre o carimbo de data/hora do evento e o relógio do sistema.
      - Aplica-se aos hubs de eventos e IoT
    - OUT_OF_ORDER_EVENT_TOLERANCE: Os eventos podem chegar fora de ordem depois de terem feito a viagem da entrada para a consulta de streaming. Esses eventos podem ser aceitos como estão, ou você pode optar por pausar por um período definido para reordená-los.
      - Aplica-se aos hubs de eventos e IoT
- **OUTPUT_OPTIONS**: Especificar opções como pares chave-valor para serviços com suporte que são saídas para consultas de streaming 
  - REJECT_POLICY:  DROP | RETRY Especifica as políticas de tratamento de erro de dados quando ocorrerem erros de conversão de dados. 
    - Aplica-se a todas as saídas com suporte 
  - MINIMUM_ROWS:  
    Mínimo de linhas necessárias por lote gravadas em uma saída. Para o Parquet, cada lote criará um novo arquivo. 
    - Aplica-se a todas as saídas com suporte 
  - MAXIMUM_TIME:  
    Tempo de espera máximo por lote. Após esse período, o lote será gravado na saída mesmo que o requisito de linhas mínimas não seja atendido. 
    - Aplica-se a todas as saídas com suporte 
  - PARTITION_KEY_COLUMN:  
    Coluna usada para a chave de partição. 
    - Aplica-se ao hub de evento e ao tópico do Barramento de Serviço 
  - PROPERTY_COLUMNS:  
    Uma lista de nomes de colunas de saída separados por vírgula que serão anexados a mensagens como propriedades personalizadas, se fornecidos.  
    - Aplica-se ao hub de eventos e à fila e ao tópico do Barramento de Serviço 
  - SYSTEM_PROPERTY_COLUMNS:  
    Uma coleção de pares de nome/valor de nomes de Propriedades do Sistema e colunas de saída formatadas como JSON a serem preenchidas nas mensagens de Barramento de Serviço. Por exemplo, { "MessageId":   "column1", "PartitionKey": "column2"} 
    - Aplica-se ao tópico e à fila do Barramento de Serviço 
  - PARTITION_KEY:  
    O nome da coluna de saída que contém a chave da partição. A chave de partição é um identificador exclusivo para a partição em uma determinada tabela que forma a primeira parte da chave primária da entidade. É um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. 
    - Aplica-se ao Armazenamento de Tabelas e ao Azure Function 
  - ROW_KEY:  
    O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo para uma entidade em uma determinada partição. Ela forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. 
    - Aplica-se ao Armazenamento de Tabelas e ao Azure Function 
  - BATCH_SIZE:  
    Isso representa o número de transações para o armazenamento de tabelas em que o máximo pode ir até 100 registros. Para o Azure Functions, isso representa o tamanho do lote em bytes enviados para a função por chamada. O padrão é 256 KB. 
    - Aplica-se ao Armazenamento de Tabelas e ao Azure Function 
  - MAXIMUM_BATCH_COUNT:  
    Número máximo de eventos enviados para a função por chamada para o Azure Function. O padrão é 100. Para o Banco de Dados SQL do Azure, isso representa o número máximo de registros enviados com cada transação de inserção em massa. O padrão é 10.000. 
    - Aplica-se ao Banco de Dados SQL do Azure SQL e ao Azure Function 
  - STAGING_AREA: Objeto EXTERNAL DATA SOURCE para armazenamento de blobs A área de preparo para a ingestão de dados de alta taxa de transferência no SQL Data Warehouse 
    - Aplica-se ao SQL Data Warehouse


## <a name="examples"></a>Exemplos

### <a name="example-1---edgehub"></a>Exemplo 1 – Hub do Edge

Tipo: Entrada ou Saída<br>
Parâmetros:
- Entrada ou Saída
  - Alias 
  - Formato de serialização do evento 
  - Codificação 
- Somente entrada: 
  - Tipo de compactação do evento 

Sintaxe:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Exemplo 2 – Banco de Dados SQL do Azure, SQL do Azure no Edge, SQL Server

Tipo: Saída<br>
Parâmetros:
- Alias de saída  
- Banco de dados (obrigatório para o Banco de Dados SQL do Azure) 
- Servidor (necessário para o Banco de Dados SQL do Azure) 
- Nome de usuário (necessário para o Banco de Dados SQL do Azure) 
- Senha (obrigatória para o Banco de Dados SQL do Azure) 
- Tabela 
- Mesclar todas as partições de entrada em um único esquema de gravação ou herdar partição da entrada ou etapa de consulta anterior (obrigatório para o Banco de Dados SQL do Azure) 
- Contagem máxima do lote 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>Exemplo 3 – Kafka

Tipo: Entrada<br>
Parâmetros:

- Servidor de inicialização Kafka 
- Nome do tópico Kafka 
- Contagem da partição de origem 

Sintaxe:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>Exemplo 4 – Armazenamento de blobs

Tipo: Entrada ou Saída<br>
Parâmetros:
- Entrada ou Saída:
  - Alias 
  - Conta de armazenamento 
  - Chave da conta de armazenamento 
  - Contêiner 
  - Padrão de caminho 
  - Formato de data 
  - Formato de hora 
  - Formato de serialização do evento 
  - Codificação 
- Somente entrada: 
  - Partições (entrada) 
  - Tipo de compactação de evento (entrada) 
- Somente saída: 
  - Mínimo de linhas (saída) 
  - Tempo máximo (saída) 
  - Modo de autenticação (saída) 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>Exemplo 5 – Hub de eventos

Tipo: Entrada ou Saída<br>
Parâmetros:
- Entrada ou Saída:
  - Alias 
  - Namespace do Barramento de Serviço 
  - Nome do Hub de Eventos 
  - Nome da política do Hub de Eventos 
  - Chave de política do Hub de Eventos 
  - Formato de serialização do evento 
  - Codificação 
- Somente entrada: 
  - Grupo de consumidores do Hub de Eventos 
  - Tipo de compactação do evento 
- Somente saída: 
  - Coluna de chave da partição 
  - Colunas da propriedade 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>Exemplo 6 – Hub IoT

Tipo: Entrada<br>
Parâmetros:

- Alias de entrada 
- Hub IoT 
- Ponto de extremidade 
- Nome da política de acesso compartilhado 
- Chave da política de acesso compartilhado 
- Grupo de consumidores 
- Formato de serialização do evento 
- Codificação 
- Tipo de compactação do evento 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>Exemplo 7 – Azure Synapse Analytics (antigo SQL Data Warehouse)

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- Banco de dados 
- Servidor 
- Nome de Usuário 
- Senha 
- Tabela 
- Área de preparo (para cópia) 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>Exemple 8 – Armazenamento de tabela

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- Conta de armazenamento 
- Chave da conta de armazenamento 
- Nome da tabela 
- Chave de partição 
- Chave de linha 
- Tamanho do lote 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Exemplo 9 – Tópico do Barramento de Serviço (mesmas propriedades como fila)

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- Namespace do Barramento de Serviço 
- Nome do tópico 
- Nome da política de tópico 
- Chave de política de tópico 
- Colunas da propriedade 
- Colunas da propriedade do sistema 
- Formato de serialização do evento 
- Codificação 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>Exemplo 10 – Cosmos DB

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- ID da Conta 
- Chave de conta 
- Banco de dados 
- Nome do contêiner 
- ID do documento 


Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>Exemplo 11 – Power BI

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- Nome do conjunto de dados 
- Nome da tabela 


Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>Exemplo 12 – Azure Function

Tipo: Saída<br>
Parâmetros:
- Aplicativo de funções 
- Função 
- Chave 
- Tamanho máximo do lote 
- Contagem máxima do lote 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>Confira também

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 

