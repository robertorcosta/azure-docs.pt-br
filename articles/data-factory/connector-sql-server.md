---
title: Copiar dados de e para o SQL Server
description: Saiba como mover dados para e para o banco de dados SQL Server que está no local ou em uma VM Azure usando a Fábrica de Dados Do Azure.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 063ac32c98d4eb64b676247c0a16f98fa7d1702d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416695"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copiar dados de e para o SQL Server usando a fábrica de dados do Azure

> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sqlserver-connector.md)
> * [Versão atual](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia na Fábrica de Dados Do Azure para copiar dados de e para um banco de dados do SQL Server. Ele se baseia no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector SQL Server é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Você pode copiar dados de um banco de dados do SQL Server para qualquer armazenamento de dados do sink suportado. Ou, você pode copiar dados de qualquer armazenamento de dados de origem suportada para um banco de dados SQL Server. Para obter uma lista de armazenamentos de dados que são suportados como fontes ou afundantes pela atividade de cópia, consulte a tabela [armazenamento de dados suportado.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector do SQL Server dá suporte a:

- As versões do SQL Server 2016, 2014, 2012, 2008 R2, 2008 e 2005.
- Copiar dados usando a autenticação do SQL ou do Windows.
- Como fonte, recuperando dados usando uma consulta SQL ou um procedimento armazenado.
- Como o coletor, acrescentar dados a uma tabela de destino ou à invocação de um procedimento armazenado com lógica personalizada durante a cópia.

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) não é suportado.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) não é suportado por este conector agora. Para contornar, você pode usar um [conector ODBC genérico](connector-odbc.md) e um driver ODBC do SQL Server. Siga [esta orientação](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) com as configurações de download de driver oDBC e string de conexão.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades usadas para definir entidades de fábrica de dados específicas do conector de banco de dados SQL Server.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado ao SQL Server:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida como **SqlServer**. | Sim |
| connectionString |Especifique as informações de **conexãoString** necessárias para se conectar ao banco de dados do SQL Server usando autenticação SQL ou autenticação do Windows. Consulte os exemplos a seguir.<br/>Você também pode colocar uma senha no Azure Key Vault. Se for autenticação SQL, `password` puxe a configuração para fora da seqüência de conexão. Para obter mais informações, consulte o exemplo JSON seguindo a tabela e [as credenciais da loja no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| userName |Especifique um nome de usuário se você usar a autenticação do Windows. Um exemplo é **nome_do_domínio\\nome_de_usuário**. |Não |
| password |Especifique uma senha para a conta de usuário especificada para o nome de usuário. Marque este campo como **SecureString** para armazená-lo com segurança na Fábrica de Dados Do Azure. Ou, você pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não |
| connectVia | Esse [Integration Runtime](concepts-integration-runtime.md) é usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos.](#prerequisites) Se não for especificado, o tempo de execução de integração padrão do Azure é usado. |Não |

>[!TIP]
>Se você acertar um erro com o código de erro "UserErrorFailedToConnectToConnectSqlServer" e uma mensagem `Pooling=false` como "O limite de sessão para o banco de dados é XXX e foi alcançado", adicione à sua seqüência de conexão e tente novamente.

**Exemplo 1: Use autenticação SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: Use autenticação SQL com uma senha no Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
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

**Exemplo 3: Use a autenticação do Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados SQL Server.

Para copiar dados de e para um banco de dados do SQL Server, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como **SqlServerTable**. | Sim |
| esquema | Nome do esquema. |Não para fonte, Sim para o coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, Sim para o coletor  |
| tableName | Nome da tabela/exibição com esquema. Esta propriedade é suportada para compatibilidade retrógrada. Para nova carga `schema` `table`de trabalho, use e . | Não para fonte, Sim para o coletor |

**Exemplo**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para uso para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte e sink do SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server como fonte

Para copiar dados do SQL Server, defina o tipo de origem na atividade de cópia como **SqlSource**. As propriedades a seguir têm suporte na seção de origem da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de atividade de cópia deve ser definida como **SqlSource**. | Sim |
| sqlReaderQuery |Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Essa propriedade é o nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Esses parâmetros são para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e a carcaça dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |Não |
| isolationLevel | Especifica o comportamento de bloqueio de transações para a fonte SQL. Os valores permitidos são: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para obter mais detalhes. | Não |

**Pontos a observar:**

- Se **sqlReaderQuery** for especificado para **SqlSource,** a atividade de cópia será executada nesta consulta contra a fonte do SQL Server para obter os dados. Você também pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** se o procedimento armazenado usa parâmetros.
- Se você não especificar **sqlReaderQuery** ou **sqlReaderStoredProcedureName,** as colunas definidas na seção "estrutura" do conjunto de dados JSON são usadas para construir uma consulta. A consulta `select column1, column2 from mytable` é executada contra o SQL Server. Se a definição de conjunto de dados não tiver "structure", todas as colunas serão selecionadas da tabela.

**Exemplo: Use a consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemplo: Use um procedimento armazenado**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**A definição do procedimento armazenado**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server como uma pia

> [!TIP]
> Saiba mais sobre os comportamentos de gravação suportados, configurações e práticas recomendadas da [Melhor prática para carregar dados no SQL Server](#best-practice-for-loading-data-into-sql-server).

Para copiar dados para o SQL Server, defina o tipo de coletor na atividade de cópia como **SqlSink**. Há suporte para as seguintes propriedades na seção do coletor da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo do dissipador de atividade de cópia deve ser definida como **SqlSink**. | Sim |
| writeBatchSize |Número de linhas a serem inseridas na tabela SQL *por lote*.<br/>Os valores permitidos são inteiros para o número de linhas. Por padrão, a Fábrica de Dados Do Azure determina dinamicamente o tamanho do lote apropriado com base no tamanho da linha. |Não |
| writeBatchTimeout |Essa propriedade especifica o tempo de espera para a operação de inserção em lotes a ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são para o tempo. Um exemplo é "00:30:00" por 30 minutos. Se nenhum valor for especificado, o tempo de tempo será padrão para "02:00:00". |Não |
| preCopyScript |Esta propriedade especifica uma consulta SQL para a atividade de cópia a ser executada antes de escrever dados no SQL Server. É chamado apenas uma vez por execução de cópia. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem em uma tabela de destino. <br/>Este procedimento armazenado é *chamado por lote*. Para operações que são executadas apenas uma vez e não têm nada `preCopyScript` a ver com dados de origem, por exemplo, excluir ou truncar, use a propriedade. | Não |
| storedProcedureTableTypeParameterNome |O nome do parâmetro do tipo de tabela especificado no procedimento armazenado.  |Não |
| sqlWriterTableType |O nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome e valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não |
| Tableoption | Especifica se criará automaticamente a tabela de sumidouros se não existir com base no esquema de origem. A criação da tabela automática não é suportada quando o dissipador especifica o procedimento armazenado ou a cópia encenada é configurada na atividade de cópia. Os valores `none` permitidos `autoCreate`são: (padrão), . |Não |

**Exemplo 1: Dados de apêndice**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Exemplo 2: Invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes em [Invocar um procedimento armazenado de um coletor SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Melhor prática para carregar dados no SQL Server

Quando você copia dados no SQL Server, você pode exigir um comportamento de gravação diferente:

- [Anexo](#append-data): Meus dados de origem têm apenas novos registros.
- [Upsert](#upsert-data): Meus dados de origem têm inserções e atualizações.
- [Sobregravação](#overwrite-the-entire-table): Eu quero recarregar toda a tabela de dimensões cada vez.
- [Escreva com lógica personalizada](#write-data-with-custom-logic): preciso de processamento extra antes da inserção final na tabela de destino.

Consulte as respectivas seções de como configurar na Fábrica de Dados Do Azure e nas práticas recomendadas.

### <a name="append-data"></a>Acrescentar dados

Anexar dados é o comportamento padrão deste conector de dissipador do SQL Server. A Azure Data Factory faz uma inserção em massa para escrever na sua mesa de forma eficiente. Você pode configurar a fonte e afundar de acordo na atividade de cópia.

### <a name="upsert-data"></a>Upsert data

**Opção 1:** Quando você tiver uma grande quantidade de dados para copiar, use a seguinte abordagem para fazer um upsert: 

- Primeiro, use uma [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregar todos os registros usando a atividade de cópia. Como as operações contra mesas temporárias não são registradas, você pode carregar milhões de registros em segundos.
- Execute uma atividade de procedimento armazenada na Fábrica de Dados Do Azure para aplicar uma instrução [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou INSERT/UPDATE. Use a tabela temporária como fonte para executar todas as atualizações ou inserções como uma única transação. Dessa forma, o número de ida e volta e operações de log é reduzido. No final da atividade de procedimento armazenada, a tabela temporária pode ser truncada para estar pronta para o próximo ciclo upsert.

Como exemplo, na Fábrica de Dados do Azure, você pode criar um pipeline com uma **atividade Copy** encadeada com uma **atividade de procedimento armazenado**. O primeiro copia dados do seu armazenamento de origem em uma tabela temporária de banco de dados, por exemplo, **##UpsertTempTable**, como o nome da tabela no conjunto de dados. Em seguida, este último invoca um procedimento armazenado para mesclar dados de origem da tabela temporária na tabela de destino e limpar a tabela temporária.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Em seu banco de dados, defina um procedimento armazenado com lógica de MERGE, como o exemplo a seguir, que é apontado a partir da atividade de procedimento armazenado anteriormente. Suponha que o alvo seja a tabela **Marketing** com três colunas: **ProfileID**, **State**e **Category**. Faça o upsert com base na coluna **ProfileID.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opção 2:** Você também pode optar por [invocar um procedimento armazenado dentro da atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Essa abordagem executa cada linha na tabela de origem em vez de usar a inserção em massa como a abordagem padrão na atividade de cópia, o que não é apropriado para upsert em grande escala.

### <a name="overwrite-the-entire-table"></a>Sobrepor toda a tabela

Você pode configurar a propriedade **preCopyScript** em um dissipador de atividades de cópia. Neste caso, para cada atividade de cópia que é executada, a Fábrica de Dados Do Azure executa o script primeiro. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, especifique um script para primeiro excluir todos os registros antes de carregar em massa os novos dados da fonte.

### <a name="write-data-with-custom-logic"></a>Escreva dados com lógica personalizada

As etapas para gravar dados com lógica personalizada são semelhantes às descritas na seção [de dados Upsert.](#upsert-data) Quando você precisa aplicar processamento extra antes da inserção final dos dados de origem na tabela de destino, para grande escala, você pode fazer uma de duas coisas: 

- Carregue em uma tabela temporária e, em seguida, invoque um procedimento armazenado. 
- Invoque um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado de um coletor SQL

Quando você copia dados em um banco de dados do SQL Server, você também pode configurar e invocar um procedimento armazenado especificado pelo usuário com parâmetros adicionais. O recurso de procedimento armazenado aproveita [parâmetros avaliados em tabela](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Invocar um procedimento armazenado processa a linha de dados por linha em vez de usar uma operação em massa, que não recomendamos para cópia em larga escala. Saiba mais [sobre as práticas recomendadas para carregar dados no SQL Server](#best-practice-for-loading-data-into-sql-server).

Você pode usar um procedimento armazenado quando os mecanismos internos de cópia não atendem à finalidade. Um exemplo é quando você deseja aplicar processamento extra antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos extras de processamento são quando você deseja mesclar colunas, procurar valores adicionais e inserir dados em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert em uma tabela no banco de dados do SQL Server. Suponha que os dados de entrada e a tabela **de marketing** do dissipador tenham cada uma três colunas: **ProfileID**, **State**e **Category**. Faça o upsert com base na coluna **ProfileID** e aplique-o apenas para uma categoria específica chamada "ProductA".

1. Em seu banco de dados, defina o tipo de tabela com o mesmo nome **de sqlWriterTableType**. O esquema do tipo de tabela é o mesmo que o esquema retornado pelos dados de entrada.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Em seu banco de dados, defina o procedimento armazenado com o mesmo nome **de sqlWriterStoredProcedureName**. Ele manipula os dados de entrada da sua origem especificada e mescla na tabela de saída. O nome do parâmetro do tipo de tabela no procedimento armazenado é o mesmo que **a tabelaName** definida no conjunto de dados.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Na Fábrica de Dados do Azure, defina a seção **de pia SQL** na atividade de cópia da seguinte forma:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mapeamento de tipo de dados para SQL Server

Quando você copia dados de e para o SQL Server, os seguintes mapeamentos são usados de tipos de dados do SQL Server para os tipos de dados provisórios do Azure Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do SQL Server | Tipo de dados provisório do Azure Data Factory |
|:--- |:--- |
| BIGINT |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Objeto |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Para tipos de dados que mapeiam para o tipo provisório Decimal, no momento, o Azure Data Factory dá suporte à precisão de até 28. Se você tiver dados que exijam precisão maior que 28, considere converter para uma cadeia de caracteres em uma consulta SQL.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Obter propriedades de atividade do Metadata

Para saber detalhes sobre as propriedades, verifique a [atividade getMetadata](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Solucionar problemas de conexão

1. Configure a instância do SQL Server para aceitar conexões remotas. Inicie **o SQL Server Management Studio,** clique com o botão direito do **mouse**e selecione **Propriedades**. Selecione **Conexões** na lista e selecione **as conexões remotas Permitir nesta** caixa de seleção do servidor.

    ![Habilitar conexões remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Para obter etapas detalhadas, [consulte Configure a opção de configuração do servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx).

2. Inicie **o Gerenciador de configuração do servidor SQL**. Expanda **Configuração de Rede do SQL Server** para a instância desejada e selecione **Protocolos para MSSQLSERVER**. Os protocolos aparecem no painel direito. Habilite o TCP/IP clicando com o botão direito do **mouse TCP/IP** e selecionando **Habilitar .**

    ![Habilitar TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Para obter mais informações e formas alternativas de habilitar o protocolo TCP/IP, consulte [Ativar ou desativar um protocolo de rede de servidor](https://msdn.microsoft.com/library/ms191294.aspx).

3. Na mesma janela, clique duas vezes em **TCP/IP** para iniciar a janela **Propriedades TCP/IP.**
4. Alterne para a guia **Endereços IP.** Role para baixo para ver a seção **IPAll.** Anote a **porta TCP**. O padrão é **1433**.
5. Crie uma **regra para o Firewall do Windows** no computador para permitir a entrada de tráfego por essa porta. 
6. **Verificar conexão**: Para conectar ao SQL Server usando um nome totalmente qualificado, use o SQL Server Management Studio de uma máquina diferente. Um exemplo é `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados suportados como fontes e afundações pela atividade de cópia na Fábrica de Dados Do Azure, consulte [armazenamentode dados suportado](copy-activity-overview.md#supported-data-stores-and-formats).
