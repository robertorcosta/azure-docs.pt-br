---
title: Criar um trabalho de streaming no T-SQL do SQL do Azure no Edge (Versão Prévia)
description: Saiba mais sobre como criar trabalhos do Stream Analytics no SQL do Azure no Edge (Versão Prévia)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7db7f9548a3daa86a53dd37fbe088661e8b7b17e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685168"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Criar um trabalho do Stream Analytics no SQL do Azure no Edge (Versão Prévia) 

Este artigo explica como criar um trabalho de streaming no T-SQL do SQL do Azure no Edge (Versão Prévia). Para criar um trabalho de streaming no SQL no Edge, as etapas a seguir são necessárias

1. Criar os objetos de entrada e saída de fluxo externo
2. Defina a consulta de trabalho de streaming como parte da criação do trabalho de streaming.

> [!NOTE]
> Para habilitar o recurso de streaming T-SQL no SQL do Azure no Edge, habilite o TF 11515 como uma opção de inicialização ou use o comando [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql). Para obter mais informações sobre como habilitar sinalizadores de rastreamento usando o arquivo mssql.conf, consulte [Configurar usando o arquivo mssql.conf](configure.md#configure-using-mssqlconf-file). Esse requisito será removido em atualizações futuras do SQL do Azure no Edge (versão prévia).

## <a name="configure-an-external-stream-input-and-output-object"></a>Configurar o objeto de entrada ou saída de um fluxo externo

O streaming no T-SQL usa a funcionalidade de fonte de dados externa do SQL Server para definir as fontes de dados associadas às entradas e saídas do fluxo externo do trabalho de streaming. Os comandos de T-SQL a seguir são necessários para criar um objeto de entrada ou saída de um fluxo externo.

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

Além disso, no caso do SQL no Edge (ou SQL Server, SQL do Azure) que está sendo usado como um fluxo de saída, o comando de T-SQL [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) é necessário para definir as credenciais para acessar o banco de dados SQL.

### <a name="supported-input-and-output-stream-data-sources"></a>Fontes de dados de fluxo de entrada e saída com suporte

O SQL do Azure no Edge atualmente oferece suporte apenas às fontes de dados a seguir como entradas e saídas de fluxo.

| Tipo de fonte de dados | Entrada | Saída | Descrição |
|------------------|-------|--------|------------------|
| Hub do Azure IoT Edge | S | S | Fonte de dados para ler/gravar dados de streaming em um hub do Azure IoT Edge. Para obter mais informações sobre o hub do Azure IoT Edge, consulte [Hub do IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| Banco de Dados SQL | N | S | Conexão de fonte de dados para gravar dados de streaming no banco de dado SQL. O banco de dados SQL pode ser um banco de dados SQL no Edge local, um SQL Server remoto ou um banco de dados SQL do Azure|
| Armazenamento do Blobs do Azure | N | S | Fonte de dados para gravar dados em um blob em uma conta de armazenamento do Azure. |
| Kafka | S | N | Fonte de dados para ler dados de streaming de um tópico Kafka. Atualmente, esse adaptador está disponível apenas para a versão Intel/AMD do SQL do Azure no Edge e não está disponível para a versão ARM64 do SQL no Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Exemplo: Criar um objeto de entrada/saída de fluxo externo para o hub do Azure IoT Edge

O exemplo a seguir cria um objeto de fluxo externo para o hub do Edge. Para criar uma fonte de dados de entrada/saída de fluxo externo para o hub do Azure IoT Edge, primeiro você precisa criar um formato de arquivo externo para que o SQL entenda também o layout dos dados que estão sendo lidos/gravados.

1. Crie um formato de arquivo externo com o tipo de formato JSON.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Crie uma fonte de dados externa para o hub do IoT Edge. O script T-SQL abaixo cria uma conexão de fonte de dados com um hub do Edge em execução no mesmo host do docker que o SQL no Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Crie o objeto de fluxo externo para o hub do IoT Edge. O script T-SQL abaixo cria um objeto de fluxo para o hub do Edge. No caso de um objeto de fluxo do hub do Edge, o parâmetro LOCATION é o nome do tópico/canal do hub do Edge no qual está sendo feita a leitura ou gravação.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>Exemplo: Criar um banco de dados SQL de objeto de fluxo externo

O exemplo a seguir cria um objeto de fluxo externo para o banco de dados SQL no Edge local. 

1. Crie uma chave mestra no banco de dados. Isso é necessário para criptografar o segredo da credencial.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Crie uma credencial no escopo do banco de dados para acessar a fonte do SQL Server. O exemplo a seguir cria uma credencial para a fonte de dados externa com IDENTITY = “nome de usuário” e SECRET = “senha”.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Crie uma fonte de dados externa, usando CREATE EXTERNAL DATA SOURCE. O exemplo a seguir:

    * Cria uma fonte de dados externa chamada LocalSQLOutput
    * Identifica a fonte de dados externa (LOCATION = '<vendor>://<server>[:<port>]'). No exemplo, ela aponta para uma instância local do SQL no Edge.
    * Por fim, o exemplo usa a credencial criada anteriormente.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Crie o objeto de fluxo externo. O exemplo a seguir cria um objeto de fluxo externo apontando para uma tabela *dbo. TemperatureMeasurements* no banco de dados *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Criar o trabalho de streaming e as consultas de streaming

Use o procedimento armazenado do sistema **sys. sp_create_streaming_job** para definir as consultas de streaming e criar o trabalho de streaming. O procedimento armazenado **sp_create_streaming_job** usa dois parâmetros

- job_name - Nome do trabalho de streaming. Os nomes de trabalhos de streaming são exclusivos em toda a instância.
- statement - instruções de consulta de streaming baseadas na [linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

O exemplo a seguir cria um trabalho de streaming simples com uma consulta de streaming. Essa consulta lê as entradas do hub do Edge e grava em *dbo.TemperatureMeasurements* no banco de dados.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

O exemplo a seguir cria um trabalho de streaming mais complexo com várias consultas diferentes, incluindo uma consulta que usa a função interna AnomalyDetection_ChangePoint para identificar anomalias nos dados de temperatura.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Iniciar, parar, remover e monitorar trabalhos de streaming

Para iniciar um trabalho de streaming no SQL no Edge, execute o procedimento armazenado **sys. sp_start_streaming_job**. O procedimento armazenado requer que o mesmo trabalho de streaming seja iniciado, como entrada.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Para parar um trabalho de streaming no SQL no Edge, execute o procedimento armazenado **sys. sp_stop_streaming_job**. O procedimento armazenado requer que o mesmo trabalho de streaming seja parado, como entrada.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Para descartar (ou excluir) um trabalho de streaming no SQL no Edge, execute o procedimento armazenado **sys. sp_drop_streaming_job**. O procedimento armazenado requer que o mesmo trabalho de streaming seja descartado, como entrada.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Para obter o status atual de um trabalho de streaming no SQL no Edge, execute o procedimento armazenado **sys. sp_get_streaming_job**. O procedimento armazenado requer que o mesmo trabalho de streaming seja descartado, como entrada e gera o nome e o status atual do trabalho de streaming.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

O trabalho de streaming pode estar em qualquer um dos seguintes status

| Status | Descrição |
|--------| ------------|
| Criado | O trabalho de fluxo foi criado, mas ainda não foi iniciado |
| Iniciando | O trabalho de streaming está na fase inicial |
| Idle | O trabalho de streaming está em execução, porém não há nenhuma entrada para processar |
| Processando | O trabalho de streaming está em execução e processando as entradas. Indica um estado de integridade para o trabalho de streaming |
| Degradado | O trabalho de streaming está em execução, porém, houve erros de serialização/desserialização de entrada/saída não fatal durante o processamento da entrada. O trabalho de entrada continuará em execução, mas descartará as entradas que encontrarem erros |
| Parado | O trabalho de streaming foi interrompido |
| Falhou | O trabalho de streaming falhou. Em geral indica um erro fatal durante o processamento |

## <a name="next-steps"></a>Próximas etapas

- [Exibir metadados associados a trabalhos de streaming no SQL do Azure no Edge (Versão Prévia)](streaming-catalog-views.md) 
- [Criar um fluxo externo](create-external-stream-transact-sql.md)
