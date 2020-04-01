---
title: Copiar e transformar dados no Banco de Dados SQL do Azure
description: Aprenda a copiar dados para e no Banco de Dados SQL do Azure e transformar dados no Banco de Dados SQL do Azure usando o Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 8f5065a0f4a2a96a747a45f64e00e86f7990bfb8
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437787"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Copiar e transformar dados no Banco de Dados SQL do Azure usando a fábrica de dados do Azure

> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-sql-connector.md)
> * [Versão atual](connector-azure-sql-database.md)

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados do Azure para copiar dados do Banco de Dados SQL do Azure e usar o Fluxo de Dados para transformar dados no Banco de Dados SQL do Azure. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de banco de dados Azure SQL é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com tabela [de matriz de origem/pia suportada](copy-activity-overview.md)
- [Mapeamento do fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Para a atividade Copy, este conector de banco de dados SQL do Azure suporta essas funções:

- Copiando dados usando autenticação SQL e autenticação de token do Azure Active Directory (Azure AD) com um principal de serviço ou identidades gerenciadas para recursos do Azure.
- Como fonte, recuperando dados usando uma consulta SQL ou um procedimento armazenado.
- Como um dissipador, anexando dados a uma tabela de destino ou invocando um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>Banco de dados Azure SQL [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) não é suportado por este conector agora. Para contornar, você pode usar um [conector ODBC genérico](connector-odbc.md) e um driver ODBC do SQL Server através de um tempo de execução de integração auto-hospedado. Siga [esta orientação](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) com as configurações de download de driver oDBC e string de conexão.

> [!IMPORTANT]
> Se você copiar dados usando o tempo de execução de integração da Fábrica de Dados do Azure, configure um [firewall do Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para que os serviços do Azure possam acessar o servidor.
> Se você copiar dados usando um tempo de execução de integração auto-hospedado, configure o firewall do Azure SQL Server para permitir o intervalo IP apropriado. Esta gama inclui o IP da máquina que é usado para se conectar ao Banco de Dados SQL do Azure.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades usadas para definir entidades da Fábrica de Dados Do Azure específicas para um conector de banco de dados SQL do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

Essas propriedades são suportadas por um serviço vinculado ao Banco de Dados SQL do Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade ** tipo** deve ser definida como **AzureSqlDatabase**. | Sim |
| connectionString | Especifique as informações necessárias para se conectar à instância do Banco de Dados SQL do Azure para a propriedade **connectionString**. <br/>Você também pode colocar uma senha ou chave principal de serviço no Azure Key Vault. Se for autenticação SQL, `password` puxe a configuração para fora da seqüência de conexão. Para obter mais informações, consulte o exemplo JSON seguindo a tabela e [as credenciais da loja no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim, quando você usa a autenticação Azure AD com um diretor de serviço |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como **SecureString** para armazená-lo com segurança na Fábrica de Dados do Azure ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação Azure AD com um diretor de serviço |
| locatário | Especifique as informações do inquilino, como o nome de domínio ou o ID do inquilino, sob o qual seu aplicativo reside. Recupere-o pairando o mouse no canto superior direito do portal Azure. | Sim, quando você usa a autenticação Azure AD com um diretor de serviço |
| connectVia | Esse [Integration Runtime](concepts-integration-runtime.md) é usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o seu armazenamento de dados estiver localizado em uma rede privada. Se não for especificado, o tempo de execução de integração padrão do Azure é usado. | Não |

Para diferentes tipos de autenticação, consulte as seções a seguir sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação SQL](#sql-authentication)
- [Autenticação de token de aplicativo Azure AD: principal do serviço](#service-principal-authentication)
- [Autenticação de token do aplicativo Azure AD: Identidades gerenciadas para recursos do Azure](#managed-identity)

>[!TIP]
>Se você acertar um erro com o código de erro "UserErrorFailedToConnectToConnectSqlServer" e uma mensagem `Pooling=false` como "O limite de sessão para o banco de dados é XXX e foi alcançado", adicione à sua seqüência de conexão e tente novamente.

### <a name="sql-authentication"></a>Autenticação SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço vinculado que usa autenticação SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Senha no Cofre de Chaves do Azure** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para usar uma autenticação de token de aplicativo do Azure AD baseada no serviço principal, siga estas etapas:

1. [Crie um aplicativo do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) no portal do Azure. Anote o nome do aplicativo e os seguintes valores que definem o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. [Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para o seu Azure SQL Server no portal Azure se você ainda não o fez. O administrador do Azure AD deve ser um usuário do Azure AD ou um grupo do Azure AD, mas não pode ser um diretor de serviços. Essa etapa é feita para que, na próxima etapa, você possa usar uma identidade do Azure AD para criar um usuário de banco de dados contido para a entidade de serviço.

3. [ Crie usuários de banco de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para o diretor de serviços. Conecte-se ao banco de dados a partir ou ao qual você deseja copiar dados usando ferramentas como o SQL Server Management Studio, com uma identidade Azure AD que tem pelo menos QUALQUER permissão do USUÁRIO. Execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Conceda ao principal de serviço as permissões necessárias, como faria normalmente para usuários do SQL ou outros. Execute o código a seguir. Para mais opções, confira [este documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Configure um serviço vinculado do Banco de Dados SQL do Azure no Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço vinculado que usa autenticação principal de serviço

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade gerenciada para autenticação do Banco de Dados SQL do Azure. O factory designado pode acessar dados do banco de dados e copiá-los para o banco de dados usando essa identidade.

Para usar a autenticação de identidade gerenciada, siga estas etapas.

1. [Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para o seu Azure SQL Server no portal Azure se você ainda não o fez. O administrador azure AD pode ser um usuário Azure AD ou um grupo Azure AD. Se você conceder uma função de administrador ao grupo com a identidade gerenciada, ignore as etapas 3 e 4. O administrador tem acesso total ao banco de dados.

2. [Criar usuários de banco de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para a identidade gerenciada da Fábrica de Dados do Azure. Conecte-se ao banco de dados a partir ou ao qual você deseja copiar dados usando ferramentas como o SQL Server Management Studio, com uma identidade Azure AD que tem pelo menos QUALQUER permissão do USUÁRIO. Execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Conceda à Fábrica de Dados permissões necessárias de identidade gerenciada, como você normalmente faz para usuários de SQL e outros. Execute o código a seguir. Para mais opções, confira [este documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Configure um serviço vinculado do Banco de Dados SQL do Azure no Azure Data Factory.

**Exemplo**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte [Conjuntos de dados](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). 

As seguintes propriedades são suportadas para o conjunto de dados do Banco de Dados SQL do Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do **tipo** do conjunto de dados deve ser definida **como AzureSqlTable**. | Sim |
| esquema | Nome do esquema. |Não para fonte, Sim para o coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, Sim para o coletor  |
| tableName | Nome da tabela/exibição com esquema. Esta propriedade é suportada para compatibilidade retrógrada. Para nova carga `schema` `table`de trabalho, use e . | Não para fonte, Sim para o coletor |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades do conjunto de dados

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela origem e pelo coletor do Banco de Dados SQL do Azure.

### <a name="azure-sql-database-as-the-source"></a>Banco de Dados SQL do Azure como a origem

Para copiar dados do Banco de Dados SQL do Azure, as seguintes propriedades são suportadas na seção **origem da** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** da fonte de atividade de cópia deve ser definida **como AzureSqlSource**. O tipo "SqlSource" ainda é suportado para compatibilidade retrógrada. | Sim |
| sqlReaderQuery | Essa propriedade usa a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. | Não |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. | Não |
| storedProcedureParameters | Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e a carcaça dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. | Não |
| isolationLevel | Especifica o comportamento de bloqueio de transações para a fonte SQL. Os valores permitidos são: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para obter mais detalhes. | Não |

**Pontos a observar:**

- Se **o sqlReaderQuery** for especificado para **o AzureSqlSource,** a atividade de cópia será executada nesta consulta contra a fonte do Banco de Dados SQL do Azure para obter os dados. Você também pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** se o procedimento armazenado usa parâmetros.
- Se você não especificar **sqlReaderQuery** ou **sqlReaderStoredProcedureName,** as colunas definidas na seção "estrutura" do conjunto de dados JSON são usadas para construir uma consulta. A consulta `select column1, column2 from mytable` é executada no Banco de Dados SQL do Azure. Se a definição de conjunto de dados não tiver "structure", todas as colunas serão selecionadas da tabela.

#### <a name="sql-query-example"></a>Exemplo de consulta SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Exemplo de procedimento armazenado

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="stored-procedure-definition"></a>Definição do procedimento armazenado

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

### <a name="azure-sql-database-as-the-sink"></a>Banco de Dados SQL do Azure como o coletor

> [!TIP]
> Saiba mais sobre os comportamentos de gravação suportados, configurações e práticas recomendadas da [Melhor prática para carregar dados no Banco de Dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database).

Para copiar dados no Banco de Dados SQL do Azure, as seguintes propriedades são suportadas na seção **de sumidouro de** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** do dissipador de atividade de cópia deve ser definida **como AzureSqlSink**. O tipo "SqlSink" ainda é suportado para compatibilidade retrógrada. | Sim |
| writeBatchSize | Número de linhas a serem inseridas na tabela SQL *por lote*.<br/> O valor permitido é **inteiro** (número de linhas). Por padrão, a Fábrica de Dados Do Azure determina dinamicamente o tamanho do lote apropriado com base no tamanho da linha. | Não |
| writeBatchTimeout | O tempo de espera para o lote inserir operação seja concluída antes de expirar.<br/> O valor permitido é **timespan**. Um exemplo é "00:30:00" (30 minutos). | Não |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia a ser executada antes de escrever dados no Banco de Dados SQL do Azure. É chamado apenas uma vez por execução de cópia. Use essa propriedade para limpar os dados pré-carregados. | Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem em uma tabela de destino. <br/>Este procedimento armazenado é *chamado por lote*. Para operações que são executadas apenas uma vez e não têm nada `preCopyScript` a ver com dados de origem, por exemplo, excluir ou truncar, use a propriedade. | Não |
| storedProcedureTableTypeParameterNome |O nome do parâmetro do tipo de tabela especificado no procedimento armazenado.  |Não |
| sqlWriterTableType |O nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome e valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não |
| Tableoption | Especifica se criará automaticamente a tabela de sumidouros se não existir com base no esquema de origem. A criação da tabela automática não é suportada quando o dissipador especifica o procedimento armazenado ou a cópia encenada é configurada na atividade de cópia. Os valores `none` permitidos `autoCreate`são: (padrão), . |Não |
| desativaçãoMetricsCollection | A Data Factory coleta métricas como DTUs de banco de dados SQL do Azure para otimização e recomendações de desempenho de cópia. Se você está preocupado com `true` esse comportamento, especifique para desligá-lo. | Não (o padrão é `false`) |

**Exemplo 1: Dados de apêndice**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>As melhores práticas para carregar dados no Banco de Dados SQL do Azure

Quando você copia dados no Banco de Dados SQL do Azure, você pode exigir um comportamento de gravação diferente:

- [Anexo](#append-data): Meus dados de origem têm apenas novos registros.
- [Upsert](#upsert-data): Meus dados de origem têm inserções e atualizações.
- [Sobregravação](#overwrite-the-entire-table): Eu quero recarregar uma tabela de dimensões inteira cada vez.
- [Escreva com lógica personalizada](#write-data-with-custom-logic): preciso de processamento extra antes da inserção final na tabela de destino.

Consulte as respectivas seções sobre como configurar na Fábrica de Dados Do Azure e práticas recomendadas.

### <a name="append-data"></a>Acrescentar dados

Anexar dados é o comportamento padrão deste conector de dissipador de banco de dados SQL do Azure. A Azure Data Factory faz uma inserção em massa para escrever na sua mesa de forma eficiente. Você pode configurar a fonte e afundar de acordo na atividade de cópia.

### <a name="upsert-data"></a>Upsert data

**Opção 1:** Quando você tiver uma grande quantidade de dados para copiar, use a seguinte abordagem para fazer um upsert: 

- Primeiro, use uma [tabela temporária com escopo de banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) para carregar todos os registros em massa usando a atividade de cópia. Como as operações contra tabelas temporárias com escopo de banco de dados não são registradas, você pode carregar milhões de registros em segundos.
- Execute uma atividade de procedimento armazenada na Fábrica de Dados Do Azure para aplicar uma instrução [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou INSERT/UPDATE. Use a tabela temporária como fonte para executar todas as atualizações ou inserções como uma única transação. Dessa forma, o número de ida e volta e operações de log é reduzido. No final da atividade de procedimento armazenada, a tabela temporária pode ser truncada para estar pronta para o próximo ciclo upsert.

Como exemplo, na Fábrica de Dados do Azure, você pode criar um pipeline com uma **atividade Copy** encadeada com uma **atividade de procedimento armazenado**. O primeiro copia dados do seu armazenamento de origem em uma tabela temporária do Banco de Dados SQL do Azure, por exemplo, **##UpsertTempTable**, como o nome da tabela no conjunto de dados. Em seguida, este último invoca um procedimento armazenado para mesclar dados de origem da tabela temporária na tabela de destino e limpar a tabela temporária.

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

Você pode configurar a propriedade **preCopyScript** no dissipador de atividades de cópia. Neste caso, para cada atividade de cópia que é executada, a Fábrica de Dados Do Azure executa o script primeiro. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, especifique um script para primeiro excluir todos os registros antes de carregar em massa os novos dados da fonte.

### <a name="write-data-with-custom-logic"></a>Escreva dados com lógica personalizada

As etapas para gravar dados com lógica personalizada são semelhantes às descritas na seção [de dados Upsert.](#upsert-data) Quando você precisa aplicar processamento extra antes da inserção final dos dados de origem na tabela de destino, para grande escala, você pode fazer uma de duas coisas:

- Carregue em uma tabela temporária de banco de dados e, em seguida, invoque um procedimento armazenado. 
- Invoque um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado de um coletor SQL

Quando você copia dados no Banco de Dados SQL do Azure, você também pode configurar e invocar um procedimento armazenado especificado pelo usuário com parâmetros adicionais. O recurso de procedimento armazenado aproveita [parâmetros avaliados em tabela](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Invocar um procedimento armazenado processa a linha de dados por linha em vez de usar uma operação em massa, que não recomendamos para cópia em larga escala. Saiba mais [sobre as melhores práticas para carregar dados no Banco de Dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database).

Você pode usar um procedimento armazenado quando os mecanismos internos de cópia não atendem à finalidade. Um exemplo é quando você deseja aplicar processamento extra antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos extras de processamento são quando você deseja mesclar colunas, procurar valores adicionais e inserir em mais de uma tabela.

A amostra a seguir mostra como usar um procedimento armazenado para fazer um upsert em uma tabela no Banco de Dados SQL do Azure. Suponha que os dados de entrada e a tabela **de marketing** do dissipador tenham cada uma três colunas: **ProfileID**, **State**e **Category**. Faça o upsert com base na coluna **ProfileID** e aplique-o apenas para uma categoria específica chamada "ProductA".

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
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no mapeamento do fluxo de dados, você pode ler e gravar em tabelas do Banco de Dados SQL do Azure. Para obter mais informações, consulte a [transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) no mapeamento dos fluxos de dados.

### <a name="source-transformation"></a>Transformação de origem

As configurações específicas do Banco de Dados SQL do Azure estão disponíveis na guia **Opções** de Origem da transformação de origem. 

**Entrada:** Selecione se você aponta sua fonte ```Select * from <table-name>```para uma tabela (equivalente a ) ou insere uma consulta SQL personalizada.

**Consulta :** Se você selecionar Consulta no campo de entrada, digite uma consulta SQL para sua fonte. Esta configuração substitui qualquer tabela escolhida no conjunto de dados. **Ordem Por** cláusulas não são suportadas aqui, mas você pode definir uma declaração SELECT FROM completa. Você também pode usar funções de tabela definidas pelo usuário. **selecione * a partir de udfGetData()** é um UDF no SQL que retorna uma tabela. Esta consulta produzirá uma tabela de origem que você pode usar no seu fluxo de dados. Usar consultas também é uma ótima maneira de reduzir linhas para testes ou para consultas. 

* Exemplo sql:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Tamanho do lote**: Digite um tamanho de lote para repassar dados grandes em leituras.

**Nível de isolamento**: O padrão para fontes SQL no mapeamento do fluxo de dados é lido sem compromisso. Você pode alterar o nível de isolamento aqui para um desses valores:
* Leitura confirmada
* Ler não comprometido
* Leitura repetida
* Serializável
* Nenhum (ignorar o nível de isolamento)

![Nível de Isolamento](media/data-flow/isolationlevel.png "Nível de Isolamento")

### <a name="sink-transformation"></a>Transformação da pia

As configurações específicas do Azure SQL Database estão disponíveis na guia **Configurações** da transformação do dissipador.

**Método de atualização:** Determina quais operações são permitidas no destino do seu banco de dados. O padrão é permitir apenas inserções. Para atualizar, upsert ou excluir linhas, uma transformação de alter-row é necessária para marcar linhas para essas ações. Para atualizações, upserts e exclusões, uma coluna de chave ou colunas devem ser definidas para determinar qual linha alterar.

![Colunas de Chave](media/data-flow/keycolumn.png "Colunas de Chave")

O nome da coluna que você escolher como a chave aqui será usado pela ADF como parte da atualização subseqüente, upsert, delete. Portanto, você deve escolher uma coluna que existe no mapeamento Sink. Se você deseja não escrever o valor nesta coluna-chave, clique em "Pular colunas-chave de escrita".

**Ação da tabela:** Determina se deve recriar ou remover todas as linhas da tabela de destino antes de escrever.
* Nenhuma: nenhuma ação será feita à mesa.
* Recriar: A tabela será derrubada e recriada. Necessário se criar uma nova tabela dinamicamente.
* Truncate: Todas as linhas da tabela de destino serão removidas.

**Tamanho do lote**: Controla quantas linhas estão sendo escritas em cada balde. Tamanhos de lote maiores melhoram a otimização de compactação e memória, mas correm o risco de exceções de memória ao cachear dados.

**Scripts Pré e Pós SQL**: Digite scripts SQL de várias linhas que executarão antes (pré-processamento) e depois que (pós-processamento) os dados forem gravados no banco de dados sink

![pré e postar scripts de processamento SQL](media/data-flow/prepost1.png "Scripts de processamento SQL")

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapeamento do tipo de dados do Banco de Dados SQL do Azure

Quando os dados são copiados ou para o Azure SQL Database, os seguintes mapeamentos são usados dos tipos de dados do Banco de Dados SQL do Azure para os tipos de dados provisórios do Azure Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do Banco de Dados SQL do Azure | Tipo de dados provisório do Azure Data Factory |
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
|  timestamp |Byte[] |
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Para tipos de dados que mapeiam para o tipo provisório Decimal, no momento, o Azure Data Factory dá suporte à precisão de até 28. Se você tiver dados com precisão maior que 28, considere converter-se para uma seqüência de string na consulta SQL.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Obter propriedades de atividade do Metadata

Para saber detalhes sobre as propriedades, verifique a [atividade getMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados suportados como fontes e afundações pela atividade de cópia na Fábrica de Dados do Azure, consulte [armazenamentos e formatos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
