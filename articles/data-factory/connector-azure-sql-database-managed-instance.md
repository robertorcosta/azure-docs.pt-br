---
title: Copiar dados de e para a instância gerenciada do banco de dados SQL do Azure
description: Aprenda a mover dados de e para a Instância Gerenciada do Banco de Dados SQL do Azure usando o Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 11f4005e802e2a584b21903bfead2c6b9701f065
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238750"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copiar dados para e da Instância Gerenciada do Banco de Dados SQL do Azure usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para uma Instância Gerenciada do Banco de Dados SQL do Azure. Ele se baseia no artigo visão geral da [atividade do Copy](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de instância gerenciada do banco de dados Azure SQL é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Você pode copiar dados de uma Instância Gerenciada do Banco de Dados SQL do Azure para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer armazenamento de dados de origem com suporte para a instância gerenciada. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector da Instância Gerenciada do Banco de Dados SQL do Azure dá suporte a:

- Copiando dados usando autenticação SQL e autenticação de token do Azure Active Directory (Azure AD) com um principal de serviço ou identidades gerenciadas para recursos do Azure.
- Como fonte, recuperando dados usando uma consulta SQL ou um procedimento armazenado.
- Como o coletor, acrescentar dados a uma tabela de destino ou à invocação de um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>A instância gerenciada do banco de dados Do Azure SQL [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) não é suportada por este conector agora. Para contornar, você pode usar um [conector ODBC genérico](connector-odbc.md) e um driver ODBC do SQL Server através de um tempo de execução de integração auto-hospedado. Siga [esta orientação](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) com as configurações de download de driver oDBC e string de conexão.

## <a name="prerequisites"></a>Pré-requisitos

Para acessar o ponto final público da instância [gerenciada](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)do banco de dados Azure SQL, você pode usar um tempo de execução gerenciado de integração do Azure Azure. Certifique-se de ativar o ponto final público e também permitir o tráfego de ponto final público no grupo de segurança da rede para que a Fábrica de Dados Do Azure possa se conectar ao seu banco de dados. Para obter mais informações, consulte [esta orientação](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Para acessar o ponto final privado de instância gerenciada do banco de dados Do Azure SQL, configure um [tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) que possa acessar o banco de dados. Se você disponibilizar o tempo de execução de integração auto-hospedado na mesma rede virtual que sua instância gerenciada, certifique-se de que sua máquina de tempo de execução de integração esteja em uma sub-rede diferente da instância gerenciada. Se você forprovisionar seu tempo de execução de integração auto-hospedado em uma rede virtual diferente da instância gerenciada, poderá usar uma rede virtual ou uma rede virtual para conexão de rede virtual. Para obter mais informações, confira [Conectar seu aplicativo à Instância Gerenciada do Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades usadas para definir entidades da Fábrica de Dados do Azure específicas do conector de instância gerenciada do banco de dados Azure SQL.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado da Instância Gerenciada do Banco de Dados SQL do Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida **como AzureSqlMI**. | Sim |
| connectionString |Essa propriedade especifica as informações de **conexãoString** necessárias para se conectar à instância gerenciada usando autenticação SQL. Para obter mais informações, confira os exemplos a seguir. <br/>A porta padrão é a 1433. Se você estiver usando a instância gerenciada do banco de dados Azure SQL com um ponto final público, especifique explicitamente a porta 3342.<br> Você também pode colocar uma senha no Azure Key Vault. Se for autenticação SQL, `password` puxe a configuração para fora da seqüência de conexão. Para obter mais informações, consulte o exemplo JSON seguindo a tabela e [as credenciais da loja no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim, quando você usa a autenticação Azure AD com um diretor de serviço |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como **SecureString** para armazená-lo com segurança na Fábrica de Dados do Azure ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação Azure AD com um diretor de serviço |
| locatário | Especifique as informações do inquilino, como o nome de domínio ou o ID do inquilino, o qual seu aplicativo reside. Recupere-o pairando o mouse no canto superior direito do portal Azure. | Sim, quando você usa a autenticação Azure AD com um diretor de serviço |
| connectVia | Esse [Integration Runtime](concepts-integration-runtime.md) é usado para se conectar ao armazenamento de dados. Você pode usar um tempo de execução de integração auto-hospedado ou um tempo de execução de integração do Azure se a instância gerenciada tiver um ponto final público e permitir que a Fábrica de Dados do Azure o acesse. Se não for especificado, o tempo de execução de integração padrão do Azure é usado. |Sim |

Para diferentes tipos de autenticação, consulte as seções a seguir sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação SQL](#sql-authentication)
- [Autenticação de token de aplicativo Azure AD: principal do serviço](#service-principal-authentication)
- [Autenticação de token do aplicativo Azure AD: Identidades gerenciadas para recursos do Azure](#managed-identity)

### <a name="sql-authentication"></a>Autenticação SQL

**Exemplo 1: use autenticação SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: use autenticação SQL com uma senha no Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

1. Siga as etapas para [provisionar um administrador de diretório ativo do Azure para sua instância gerenciada](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Crie um aplicativo do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) no portal do Azure. Anote o nome do aplicativo e os seguintes valores que definem o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

3. [Crie logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) para a identidade gerenciada da Fábrica de Dados do Azure. No SSMS (SQL Server Management Studio), conecte-se à sua Instância Gerenciada usando uma conta do SQL Server que seja um **sysadmin**. No banco de dados **mestre**, execute o seguinte T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Criar usuários de banco de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para a identidade gerenciada da Fábrica de Dados do Azure. Conecte-se ao banco de dados do ou para o qual você deseja copiar dados. Execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Conceda à Fábrica de Dados permissões necessárias de identidade gerenciada, como você normalmente faz para usuários de SQL e outros. Execute o código a seguir. Para mais opções, confira [este documento](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Configure um serviço vinculado à instância gerenciada do banco de dados Azure SQL na Fábrica de Dados Do Azure.

**Exemplo: use a autenticação principal do serviço**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade gerenciada para autenticação de instância gerenciada do banco de dados SQL do Azure. O factory designado pode acessar dados do banco de dados e copiá-los para o banco de dados usando essa identidade.

Para usar a autenticação de identidade gerenciada, siga estas etapas.

1. Siga as etapas para [provisionar um administrador de diretório ativo do Azure para sua instância gerenciada](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Crie logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) para a identidade gerenciada da Fábrica de Dados do Azure. No SSMS (SQL Server Management Studio), conecte-se à sua Instância Gerenciada usando uma conta do SQL Server que seja um **sysadmin**. No banco de dados **mestre**, execute o seguinte T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Criar usuários de banco de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para a identidade gerenciada da Fábrica de Dados do Azure. Conecte-se ao banco de dados do ou para o qual você deseja copiar dados. Execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Conceda à Fábrica de Dados permissões necessárias de identidade gerenciada, como você normalmente faz para usuários de SQL e outros. Execute o código a seguir. Para mais opções, confira [este documento](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Configure um serviço vinculado à instância gerenciada do banco de dados Azure SQL na Fábrica de Dados Do Azure.

**Exemplo: usa autenticação de identidade gerenciada**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e das propriedades disponíveis para uso para definir conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados da Instância Gerenciada do Banco de Dados SQL do Azure.

Para copiar dados para e da instância gerenciada do Banco de Dados SQL do Azure, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida **como AzureSqlMITable**. | Sim |
| esquema | Nome do esquema. |Não para fonte, Sim para o coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, Sim para o coletor  |
| tableName | Nome da tabela/exibição com esquema. Esta propriedade é suportada para compatibilidade retrógrada. Para nova carga `schema` `table`de trabalho, use e . | Não para fonte, Sim para o coletor |

**Exemplo**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

Para obter uma lista completa das seções e propriedades disponíveis para uso para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte da fonte de dados e do coletor da Instância Gerenciada do Banco de Dados SQL do Azure.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Instância Gerenciada do Banco de Dados SQL do Azure como uma fonte de dados

Para copiar dados da Instância Gerenciada do Banco de Dados Do Azure SQL, as seguintes propriedades são suportadas na seção origem da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de atividade de cópia deve ser definida como **SqlMISource**. | Sim |
| sqlReaderQuery |Essa propriedade usa a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Essa propriedade é o nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Esses parâmetros são para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e o uso de maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| isolationLevel | Especifica o comportamento de bloqueio de transações para a fonte SQL. Os valores permitidos são: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para obter mais detalhes. | Não |

**Observe os seguintes pontos:**

- Se **sqlReaderQuery** for especificado para **SqlMISource,** a atividade de cópia será executada nesta consulta contra a fonte de instância gerenciada para obter os dados. Você também pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** se o procedimento armazenado usa parâmetros.
- Se você não especificar **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, as colunas definidas na seção "structure" do conjunto de dados JSON serão usadas para criar uma consulta. A consulta `select column1, column2 from mytable` é executada em relação à instância gerenciada. Se a definição de conjunto de dados não tiver "structure", todas as colunas serão selecionadas da tabela.

**Exemplo: Use uma consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Instância Gerenciada do Banco de Dados SQL do Azure como uma coletor

> [!TIP]
> Saiba mais sobre os comportamentos de gravação suportados, configurações e práticas recomendadas da [prática recomendada para carregar dados na instância gerenciada do banco de dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Para copiar dados para a instância gerenciada do banco de dados Do Azure SQL, as seguintes propriedades são suportadas na seção de sumidouro de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo do dissipador de atividade de cópia deve ser definida como **SqlMISink**. | Sim |
| writeBatchSize |Número de linhas a serem inseridas na tabela SQL *por lote*.<br/>Os valores permitidos são inteiros para o número de linhas. Por padrão, a Fábrica de Dados Do Azure determina dinamicamente o tamanho do lote apropriado com base no tamanho da linha.  |Não |
| writeBatchTimeout |Essa propriedade especifica o tempo de espera para a operação de inserção em lotes a ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são para o tempo. Um exemplo é "00: 30:00", que são 30 minutos. |Não |
| preCopyScript |Esta propriedade especifica uma consulta SQL para que a atividade de cópia seja executada antes de escrever dados na instância gerenciada. É chamado apenas uma vez por execução de cópia. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem em uma tabela de destino. <br/>Este procedimento armazenado é *chamado por lote*. Para operações que são executadas apenas uma vez e não têm nada `preCopyScript` a ver com dados de origem, por exemplo, excluir ou truncar, use a propriedade. | Não |
| storedProcedureTableTypeParameterNome |O nome do parâmetro do tipo de tabela especificado no procedimento armazenado.  |Não |
| sqlWriterTableType |O nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome e valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não |
| Tableoption | Especifica se criará automaticamente a tabela de sumidouros se não existir com base no esquema de origem. A criação da tabela automática não é suportada quando o dissipador especifica o procedimento armazenado ou a cópia encenada é configurada na atividade de cópia. Os valores `none` permitidos `autoCreate`são: (padrão), . |Não |

**Exemplo 1: Dados de apêndice**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Exemplo 2: Invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes [de Invocar um procedimento armazenado em uma pia SQL MI](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>As melhores práticas para carregar dados na instância gerenciada do banco de dados SQL do Azure

Quando você copia dados na instância gerenciada do banco de dados SQL do Azure, você pode exigir um comportamento de gravação diferente:

- [Anexo](#append-data): Meus dados de origem têm apenas novos registros.
- [Upsert](#upsert-data): Meus dados de origem têm inserções e atualizações.
- [Sobregravação](#overwrite-the-entire-table): Eu quero recarregar toda a tabela de dimensões cada vez.
- [Escreva com lógica personalizada](#write-data-with-custom-logic): preciso de processamento extra antes da inserção final na tabela de destino. 

Consulte as respectivas seções de como configurar na Fábrica de Dados Do Azure e nas práticas recomendadas.

### <a name="append-data"></a>Acrescentar dados

Anexar dados é o comportamento padrão deste conector de dissipar instância gerenciada do banco de dados Azure SQL. A Azure Data Factory faz uma inserção em massa para escrever na sua mesa de forma eficiente. Você pode configurar a fonte e afundar de acordo na atividade de cópia.

### <a name="upsert-data"></a>Upsert data

**Opção 1:** Quando você tiver uma grande quantidade de dados para copiar, use a seguinte abordagem para fazer um upsert: 

- Primeiro, use uma [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregar todos os registros usando a atividade de cópia. Como as operações contra mesas temporárias não são registradas, você pode carregar milhões de registros em segundos.
- Execute uma atividade de procedimento armazenada na Fábrica de Dados Do Azure para aplicar uma instrução [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou INSERT/UPDATE. Use a tabela temporária como fonte para executar todas as atualizações ou inserções como uma única transação. Dessa forma, o número de ida e volta e operações de log é reduzido. No final da atividade de procedimento armazenada, a tabela temporária pode ser truncada para estar pronta para o próximo ciclo upsert.

Como exemplo, na Fábrica de Dados do Azure, você pode criar um pipeline com uma **atividade Copy** encadeada com uma **atividade de procedimento armazenado**. O primeiro copia dados da sua loja de origem em uma tabela temporária, por exemplo, **##UpsertTempTable**, como o nome da tabela no conjunto de dados. Em seguida, este último invoca um procedimento armazenado para mesclar dados de origem da tabela temporária na tabela de destino e limpar a tabela temporária.

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

**Opção 2:** Você também pode optar por [invocar um procedimento armazenado dentro de uma atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Essa abordagem executa cada linha na tabela de origem em vez de usar a inserção em massa como a abordagem padrão na atividade de cópia, o que não é apropriado para upsert em grande escala.

### <a name="overwrite-the-entire-table"></a>Sobrepor toda a tabela

Você pode configurar a propriedade **preCopyScript** em um dissipador de atividades de cópia. Neste caso, para cada atividade de cópia que é executada, a Fábrica de Dados Do Azure executa o script primeiro. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, especifique um script para primeiro excluir todos os registros antes de carregar em massa os novos dados da fonte.

### <a name="write-data-with-custom-logic"></a>Escreva dados com lógica personalizada

As etapas para gravar dados com lógica personalizada são semelhantes às descritas na seção [de dados Upsert.](#upsert-data) Quando você precisa aplicar processamento extra antes da inserção final dos dados de origem na tabela de destino, para grande escala, você pode fazer uma de duas coisas: 

- Carregue em uma tabela temporária e, em seguida, invoque um procedimento armazenado.
- Invoque um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado de um coletor SQL

Quando você copia dados na instância gerenciada do banco de dados Do Azure SQL, você também pode configurar e invocar um procedimento armazenado especificado pelo usuário com parâmetros adicionais. O recurso de procedimento armazenado aproveita [parâmetros avaliados em tabela](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Invocar um procedimento armazenado processa a linha de dados por linha em vez de usar uma operação em massa, que não recomendamos para cópia em larga escala. Saiba mais sobre [as práticas recomendadas para carregar dados na instância gerenciada do banco de dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

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

3. Na Fábrica de Dados do Azure, defina a seção **de pia SQL MI** na atividade de cópia da seguinte forma:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapeamento do tipo de dados da Instância Gerenciada do Banco de Dados SQL do Azure

Quando dados são copiados para e da Instância Gerenciada do Banco de Dados SQL do Azure, os seguintes mapeamentos são usados de tipos de dados da Instância Gerenciada do Banco de Dados SQL do Azure para tipos de dados provisórios do Azure Data Factory. Para saber mais sobre como a atividade de cópia mapeia do tipo de dados e esquema de origem para o coletor, confira [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados da Instância Gerenciada do Banco de Dados SQL do Azure | Tipo de dados provisório do Azure Data Factory |
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

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados suportados como fontes e afundações pela atividade de cópia na Fábrica de Dados Do Azure, consulte [armazenamentode dados suportado](copy-activity-overview.md#supported-data-stores-and-formats).
