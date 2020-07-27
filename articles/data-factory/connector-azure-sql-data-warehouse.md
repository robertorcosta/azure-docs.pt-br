---
title: Copiar e transformar dados no Azure Synapse Analytics
description: Saiba como copiar dados de e para o Azure Synapse Analytics e transformar dados no Azure Synapse Analytics usando o Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: 8d7171bafb292b0520b8873bad0ce8f55ab4040d
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171509"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar e transformar dados no Azure Synapse Analytics (anteriormente SQL Data Warehouse do Azure) usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
>
> - [Versão 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Versão atual](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Azure Synapse Analytics e usar o Fluxo de Dados para transformar dados no Azure Data Lake Storage Gen2. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Azure Synapse Analytics é compatível com as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com tabela de [matriz de origem/coletor compatível](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Para a atividade de cópia, este conector do Azure Synapse Analytics é compatível com estas funções:

- Copie os dados usando a autenticação de token do Aplicativo Azure AD (Azure Active Directory) e autenticação do SQL com uma entidade de serviço ou identidades gerenciadas para recursos do Azure.
- Como uma fonte, recupere dados usando uma consulta SQL ou procedimento armazenado.
- Como um coletor, carregue dados usando o [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse), a [instrução COPY](#use-copy-statement) (versão prévia) ou, ainda, Bulk Insert. Recomendamos o PolyBase ou a instrução COPY (versão prévia) para melhor desempenho de cópia. O conector também dá suporte à criação automática da tabela de destino se não existir com base no esquema de origem.

> [!IMPORTANT]
> Se você copiar dados usando Azure Data Factory Integration Runtime, configure uma [regra de firewall no nível de servidor](../azure-sql/database/firewall-configure.md) para que os serviços do Azure possam acessar o [SQL Server lógico](../azure-sql/database/logical-servers.md).
> Se você copiar dados usando um tempo de execução de integração auto-hospedado, configure o firewall para permitir o intervalo de IP apropriado. Esse intervalo inclui o IP do computador usado para se conectar ao Azure Synapse Analytics.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter melhor desempenho, use o PolyBase para carregar dados no Azure Synapse Analytics. A seção [Usar o PolyBase para carregar dados para o Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) apresenta detalhes a respeito. Para ver um passo a passo com um caso de uso, confira [Carregar 1 TB no Azure Synapse Analytics em menos de 15 minutos com o Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que definem entidades do Data Factory específicas para um conector do Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são compatíveis com um serviço vinculado do Azure Synapse Analytics:

| Propriedade            | Descrição                                                  | Obrigatório                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | A propriedade type deve ser definida como  **AzureSqlDW**.             | Sim                                                          |
| connectionString    | Especifique as informações necessárias para conectar-se à instância do Azure Synapse Analytics para a propriedade **connectionString**. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar uma senha/chave da entidade de serviço no Azure Key Vault e se sua autenticação do SQL efetua pull da configuração da `password` da cadeia de conexão. Veja o exemplo de JSON abaixo da tabela e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) que fornece mais detalhes. | Sim                                                          |
| servicePrincipalId  | Especifique a ID do cliente do aplicativo.                         | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| locatário              | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-lo focalizando o canto superior direito do portal do Azure. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| connectVia          | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Azure Integration runtime ou um runtime de integração auto-hospedada (se o seu armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não                                                           |

Para diferentes tipos de autenticação, consulte as seções a seguir sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- Uso da autenticação de token do aplicativo Azure Active Directory: [Entidade de serviço](#service-principal-authentication)
- Uso da autenticação de token do aplicativo Azure Active Directory: [Identidades gerenciadas para os recursos do Azure](#managed-identity)

>[!TIP]
>Se ocorrer erro com código de erro como "UserErrorFailedToConnectToSqlServer" e mensagem como "O limite da sessão para o banco de dados é XXX e foi atingido.", adicione `Pooling=false` à cadeia de conexão e tente novamente.

### <a name="sql-authentication"></a>Autenticação SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço vinculado que usa autenticação SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

**Senha no Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
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

Para usar a autenticação de token de aplicativo do Azure AD com base em entidade de serviço, siga estas etapas:

1. **[Crie um aplicativo do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** no portal do Azure. Anote o nome do aplicativo e os seguintes valores que definem o serviço vinculado:

   - ID do aplicativo
   - Chave do aplicativo
   - ID do locatário

2. **[Provisione um administrador de Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** para seu servidor no portal do Azure se ainda não tiver feito isso. O administrador do Azure AD pode ser um usuário ou um grupo do Azure AD. Se você conceder uma função de administrador ao grupo com a identidade gerenciada, ignore as etapas 3 e 4. O administrador terá acesso completo ao banco de dados.

3. **[ Crie usuários de banco de dados contidos](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** para o diretor de serviços. Conecte-se ao data warehouse de ou para o qual você deseja copiar dados usando ferramentas como SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o seguinte T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceda ao principal de serviço as permissões necessárias**, como faria normalmente para usuários do SQL ou outros. Execute o código a seguir ou veja mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se você quiser usar o PolyBase para carregar os dados, aprenda a [permissão de banco de dados necessária](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configure um serviço vinculado do Azure Synapse Analytics** no Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço vinculado que usa autenticação principal de serviço

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa o factory específico. Você pode usar essa identidade gerenciada para a autenticação do Azure Synapse Analytics. A fábrica designada pode acessar e copiar dados de ou para seu data warehouse usando essa identidade.

Para usar a autenticação de identidade gerenciada, siga estas etapas:

1. **[Provisione um administrador de Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** para seu servidor no portal do Azure se ainda não tiver feito isso. O administrador do Azure AD pode ser um usuário ou um grupo do Azure AD. Se você conceder uma função de administrador ao grupo com a identidade gerenciada, ignore as etapas 3 e 4. O administrador terá acesso completo ao banco de dados.

2. **[Criar usuários de banco de dados independente](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** para a identidade gerenciada pelo data factory. Conecte-se ao data warehouse de ou para o qual você deseja copiar dados usando ferramentas como SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o seguinte T-SQL.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Conceda à identidade gerenciada pelo data factory as permissões necessárias**, como faria normalmente para usuários do SQL e outros. Execute o código a seguir ou veja mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se você quiser usar o PolyBase para carregar os dados, aprenda a [permissão de banco de dados necessária](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. **Configure um serviço vinculado do Azure Synapse Analytics** no Azure Data Factory.

**Exemplo:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md).

As seguintes propriedades são compatíveis com um conjunto de dados do Azure Synapse Analytics:

| Propriedade  | Descrição                                                  | Obrigatório                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | A propriedade **tipo** do conjunto de dados deve ser definida como  **AzureSqlDWTable**. | Sim                         |
| esquema | Nome do esquema. |Não para fonte, Sim para o coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, Sim para o coletor  |
| tableName | Nome da tabela/exibição com esquema. Essa propriedade é compatível com versões anteriores. Para uma nova carga de trabalho, use `schema` e `table`. | Não para fonte, Sim para o coletor |

### <a name="dataset-properties-example"></a>Exemplo de propriedades do conjunto de dados

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades compatíveis com a origem e o coletor do Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics como a origem

Para copiar dados do Azure Synapse Analytics, defina a propriedade **type** na origem da Atividade de Cópia para **SqlDWSource**. As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade                     | Descrição                                                  | Obrigatório |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | A propriedade **tipo** da origem da Atividade de Cópia deve ser configurada para **SqlDWSource**. | Sim      |
| sqlReaderQuery               | Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. | Não       |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. | Não       |
| storedProcedureParameters    | Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não       |
| isolationLevel | Especifica o comportamento de bloqueio de transação para a origem do SQL. Os valores permitidos são: **ReadCommitted**, **READUNCOMMITTED**, **REPEATABLEREAD**, **Serializable**, **snapshot**. Se não for especificado, o nível de isolamento padrão do banco de dados será usado. Veja [este documento](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para obter mais detalhes. | Não |

**Exemplo: usando a consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemplo: usando o procedimento armazenado**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**Exemplo de procedimento armazenado:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure Synapse Analytics como coletor

O Azure Data Factory dá suporte a três maneiras de carregar dados no SQL Data Warehouse.

![Opções de cópia do coletor do SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Usar o PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse)
- [Usar a instrução COPY (versão prévia)](#use-copy-statement)
- Usar Bulk Insert

A maneira mais rápida e escalonável de carregar dados é por meio do [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) ou da [instrução COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (versão prévia).

Para copiar dados para o SQL Data Warehouse do Azure, defina o tipo de coletor em Atividade de Cópia para **SqlDWSink**. As seguintes propriedades são suportadas na seção Copy Activity **sink**:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | A propriedade **tipo** do coletor de Atividade de Cópia deve ser definida como **SqlDWSink**. | Sim                                           |
| allowPolyBase     | Indica se o PolyBase será ou não usado para carregar dados no SQL Data Warehouse. `allowCopyCommand` e `allowPolyBase` não podem ser ambos verdadeiros. <br/><br/>Confira a seção [Usar o PolyBase para carregar dados no Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter os detalhes e as restrições.<br/><br/>Os valores permitidos são **True** e **False** (padrão). | Não.<br/>Aplicar ao usar o PolyBase.     |
| polyBaseSettings  | Um grupo de propriedades que pode ser especificado quando a propriedade `allowPolybase` está definida como **true**. | Não.<br/>Aplicar ao usar o PolyBase. |
| allowCopyCommand | Indica se a [instrução COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (versão prévia) será ou não usada para carregar dados no SQL Data Warehouse. `allowCopyCommand` e `allowPolyBase` não podem ser ambos verdadeiros. <br/><br/>Confira a seção [Usar a instrução COPY para carregar dados no SQL Data Warehouse do Azure](#use-copy-statement) para obter os detalhes e as restrições.<br/><br/>Os valores permitidos são **True** e **False** (padrão). | Não.<br>Aplicar ao usar a instrução COPY. |
| copyCommandSettings | Um grupo de propriedades que pode ser especificado quando a propriedade `allowCopyCommand` está definida como TRUE. | Não.<br/>Aplicar ao usar a instrução COPY. |
| writeBatchSize    | Número de linhas a serem inseridas na tabela SQL **por lote**.<br/><br/>O valor permitido é **inteiro** (número de linhas). Por padrão, o Data Factory determina dinamicamente o tamanho do lote apropriado com base no tamanho da linha. | Não.<br/>Aplicar ao usar Bulk Insert.     |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lote ser concluída antes de expirar.<br/><br/>O valor permitido é **timespan**. Exemplo: "00:30:00" (30 minutos). | Não.<br/>Aplicar ao usar Bulk Insert.        |
| preCopyScript     | Especifique uma consulta SQL para que a Atividade de Cópia seja executada antes de gravar dados no Azure SQL Data Warehouse em cada execução. Use essa propriedade para limpar os dados pré-carregados. | Não                                            |
| tableOption | Especifica se a [tabela do coletor deve ser criada automaticamente](copy-activity-overview.md#auto-create-sink-tables) se não existir com base no esquema de origem. A criação automática de tabela não é um procedimento compatível quando a cópia preparada está configurada na atividade de cópia. Os valores permitidos são `none` (padrão) e `autoCreate`. |Não |
| disableMetricsCollection | O Data Factory coleta métricas como DWUs do SQL Data Warehouse para recomendações e otimização de desempenho de cópia. Se você estiver preocupado com esse comportamento, especifique `true` para desativá-lo. | Não (o padrão é `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Exemplo de coletor do SQL Data Warehouse

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Use PolyBase to load data into Azure SQL Data Warehouse

Usar o [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) é uma forma eficiente de carregar uma grande quantidade de dados no Azure Synapse Analytics com taxa de transferência alta. Você verá um grande ganho na taxa de transferência usando PolyBase em vez do mecanismo BULKINSERT padrão. Para obter um passo a passo com um caso de uso, confira [Carregar 1 TB no Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Se os dados de origem estiverem no **Blob do Azure, Azure Data Lake Storage Gen1 ou Azure Data Lake Storage Gen2** e o formato **for compatível com o polybase**, você poderá usar a atividade de cópia para invocar diretamente o PolyBase para permitir que o Azure SQL Data Warehouse efetue pull dos dados da origem. Para detalhes, veja **[ Cópia direta usando PolyBase ](#direct-copy-by-using-polybase)** .
- Se o armazenamento e o formato de dados de origem não forem originalmente suportados pelo PolyBase, use a **[cópia Staged usando o recurso PolyBase ](#staged-copy-by-using-polybase)** . O recurso de cópia preparada também oferece melhor rendimento. Ele converte automaticamente os dados em formato compatível com o polybase, armazena os dados no armazenamento de BLOBs do Azure e, em seguida, chama o polybase para carregar dados em SQL Data Warehouse.

> [!TIP]
> Saiba mais sobre as [Melhores práticas para usar o PolyBase](#best-practices-for-using-polybase).

As seguintes configurações do PolyBase são compatíveis em `polyBaseSettings` na atividade de cópia:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Especifica o número ou o percentual de linhas que podem ser rejeitadas antes de a consulta falhar.<br/><br/>Saiba mais sobre as opções de rejeição do PolyBase na seção Argumentos de [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Os valores permitidos são 0 (padrão), 1, 2 etc. | Não                                            |
| rejectType        | Especifica se a opção **rejectValue** é um valor literal ou uma porcentagem.<br/><br/>Os valores permitidos são **Valor** (padrão) e **Porcentagem**. | Não                                            |
| rejectSampleValue | Determina o número de linhas a serem recuperadas antes que o PolyBase recalcule a porcentagem de linhas rejeitadas.<br/><br/>Os valores permitidos são 1, 2 etc. | Sim, se o **rejectType** for  **porcentagem**. |
| useTypeDefault    | Especifica como tratar valores ausentes em arquivos de texto delimitados quando o PolyBase recuperar dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção Argumentos em [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Os valores permitidos são **True** e **False** (padrão).<br><br> | Não                                            |

### <a name="direct-copy-by-using-polybase"></a>Cópia direta usando PolyBase

O PolyBase do SQL Data Warehouse é diretamente compatível com o Blob do Azure, o Azure Data Lake Storage Gen1 e o Azure Data Lake Storage Gen2. Se os dados de origem atenderem aos critérios descritos nesta seção, use o PolyBase para copiar diretamente do armazenamento de dados de origem para o Azure Synapse Analytics. Caso contrário, use [cópia Staged usando PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Para copiar dados de maneira eficiente para o SQL Data Warehouse, saiba mais em [O Azure Data Factory torna ainda mais fácil e conveniente descobrir percepções dos dados ao usar o Data Lake Storage com o SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem atendidos, o Azure Data Factory verificará as configurações e retornará automaticamente ao mecanismo BULKINSERT para a movimentação de dados.

1. O **serviço vinculado de origem** acompanha os seguintes tipos e métodos de autenticação:

    | Tipo de armazenamento de dados de origem compatíveis                             | Tipos de autenticação de origem compatíveis                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | Autenticação de chave de conta, autenticação de identidade gerenciada |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autenticação de entidade de serviço                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autenticação de chave de conta, autenticação de identidade gerenciada |

    >[!IMPORTANT]
    >Se o Armazenamento do Azure estiver configurado com o ponto de extremidade de serviço de VNet, você precisará usar a autenticação de identidade gerenciada – veja [Impacto de usar pontos de extremidade de serviço de VNet com Armazenamento do Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias no Data Factory das seções [Blob do Azure – autenticação de identidade gerenciada](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 – autenticação de identidade gerenciada](connector-azure-data-lake-storage.md#managed-identity), respectivamente.

2. O **formato de dados de origem** é de **Parquet**, **ORC** ou **Texto delimitado**, com as seguintes configurações:

   1. O caminho da pasta não contém o filtro curinga.
   2. O nome do arquivo está vazio ou aponta para apenas um arquivo. Se você especificar o nome de arquivo curinga na atividade de cópia, ele só poderá ser `*` ou `*.*`.
   3. `rowDelimiter` é **padrão**, **\n**, **\r\n** ou **\r**.
   4. `nullValue` é deixado como padrão ou definido como **cadeia de caracteres vazia** ("") e `treatEmptyAsNull` é deixado como padrão ou definido como true.
   5. `encodingName` é deixado como padrão ou definido como **utf-8**.
   6. `quoteChar`, `escapeChar` e `skipLineCount` não são especificadas. O PolyBase é compatível com a opção de ignorar a linha de cabeçalho, que pode ser configurada como `firstRowAsHeader` no ADF.
   7. `compression` pode ser **sem compactação**, **GZip** ou **Deflate**.

3. Se a sua origem for uma pasta, `recursive` na atividade de cópia precisará ser definida como true.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` e `additionalColumns` não foram especificados.

>[!NOTE]
>Se a sua origem for uma pasta, o PolyBase recuperará arquivos da pasta e de todas as respectivas subpastas e não recuperará dados de arquivos para os quais o nome do arquivo começa com um sublinhado (_) ou um ponto final (.), conforme documentado [aqui – argumento LOCATION](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Cópia organizada usando PolyBase

Quando os dados de origem não são nativamente compatíveis com o PolyBase, habilite a cópia de dados por meio de uma instância do Armazenamento de Blobs do Azure de preparo provisório (não pode ser o Armazenamento Premium do Azure). Nesse caso, o Azure Data Factory converte automaticamente os dados para atender aos requisitos de formato de dados do PolyBase. Em seguida, ele invoca o PolyBase para carregar os dados no SQL Data Warehouse. Finalmente, ele limpa seus dados temporários do armazenamento de blobs. Consulte [Cópia preparada](copy-activity-performance-features.md#staged-copy) para obter detalhes sobre a cópia de dados por meio de uma instância de armazenamento de Blob do Azure de preparação.

Para usar esse recurso, crie um [serviço vinculado do Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#linked-service-properties) que se refira à conta de Armazenamento do Azure com o Armazenamento de Blobs provisório. Em seguida, especifique as propriedades `enableStaging` e `stagingSettings` para a atividade de cópia, conforme mostrado no código a seguir.

>[!IMPORTANT]
>Se o Armazenamento do Azure de preparo estiver configurado com o ponto de extremidade de serviço de VNet, você precisará usar a autenticação de identidade gerenciada – veja [Impacto de usar pontos de extremidade de serviço de VNet com Armazenamento do Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias em Data Factory de [Blob do Azure – autenticação de identidade gerenciada](connector-azure-blob-storage.md#managed-identity).

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Práticas recomendadas para usar o PolyBase

As seções a seguir fornecem práticas recomendadas além daquelas mencionadas em [Práticas recomendadas para o Azure Synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Permissão de banco de dados obrigatória

Para usar o PolyBase, o usuário que carrega dados no SQL Data Warehouse deve ter a [permissão "CONTROLE"](https://msdn.microsoft.com/library/ms191291.aspx) no banco de dados de destino. Uma maneira de conseguir isso é adicionar o usuário como membro da função **db_owner**. Aprenda como fazer isso na [visão geral do SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Limites de tamanho de linha e tipo de dados

As cargas do PolyBase são limitadas a linhas menores que 1 MB. Ele não pode ser usado para carregar para VARCHR (MAX), NVARCHAR (MAX) ou VARBINARY (MAX). Para obter mais informações, consulte [Limites de capacidade do serviço SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando os dados de origem tiverem linhas maiores que 1 MB, talvez você queira dividir verticalmente as tabelas de origem em várias pequenas. Certifique-se de que o maior tamanho de cada linha não exceda o limite. As tabelas menores podem ser carregadas por meio de PolyBase e mescladas no Azure Synapse Analytics.

Como alternativa, para dados com colunas tão largas assim, você pode carregar os dados usando o ADF, sem o PolyBase. Para isso, desative a configuração "permitir PolyBase".

#### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse

Para obter a melhor taxa de transferência possível, atribua uma classe de recurso maior para o usuário que carrega dados em SQL Data Warehouse por meio do PolyBase.

#### <a name="polybase-troubleshooting"></a>Solucionando problemas do PolyBase

**Carregando na coluna Decimal**

Se os dados de origem estiverem em formato de texto ou em outros repositórios compatíveis que não sejam o PolyBase (usando cópia em etapas e o PolyBase) e contiverem um valor vazio a ser carregado na coluna Decimal do SQL Data Warehouse, você poderá encontrar o seguinte erro:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A solução é desmarcar a opção "**Usar o tipo padrão**" (como false) no coletor da atividade de cópia -> configurações do PolyBase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" é uma configuração nativa do PolyBase, que especifica como tratar valores ausentes em arquivos de texto delimitados quando o PolyBase recupera dados do arquivo de texto.

**`tableName` no Azure Synapse Analytics**

A tabela a seguir fornece exemplos de como especificar o **tableName** propriedade no conjunto de dados JSON. Ele mostra as várias combinações de nomes de esquema e tabela.

| Esquema do BD | Nome da tabela | **tableName** propriedade JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable ou dbo.MyTable ou [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable ou [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] ou [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1]. [My.Table]                         |

Se você vir o erro a seguir, o problema pode ser o valor especificado para o **tableName** propriedade. Consulte a tabela anterior para a maneira correta especificar valores para o **tableName** propriedade JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Colunas com valores padrão**

No momento, o recurso PolyBase na fábrica de dados aceita apenas o mesmo número de colunas da tabela de destino. Um exemplo é uma tabela com quatro colunas em que uma delas é definida com um valor padrão. Os dados de entrada ainda precisam ter quatro colunas. Um conjunto de dados de entrada de três colunas produz um erro semelhante à seguinte mensagem:

```
All columns of the table must be specified in the INSERT BULK statement.
```

O valor NULL é uma forma especial do valor padrão. Se a coluna permite valor nulo, os dados de entrada no blob para essa coluna podem estar vazios. Mas não pode estar ausente do conjunto de dados de entrada. O PolyBase insere NULL para valores ausentes no Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a> Use a instrução COPY para carregar dados no SQL Data Warehouse do Azure (versão prévia)

A [instrução COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (versão prévia) do SQL Data Warehouse é diretamente compatível com o carregamento de dados provenientes do **Blob do Azure e do Azure Data Lake Storage Gen2**. Se os dados de origem atenderem aos critérios descritos nesta seção, você poderá escolher usar a instrução COPY no ADF para carregar dados para o SQL Data Warehouse do Azure. O Azure Data Factory verificará as configurações e falhará na execução da atividade de cópia se os critérios não forem atendidos.

>[!NOTE]
>Atualmente, o Data Factory só dá suporte à cópia de origens compatíveis com a instrução COPY, conforme mencionado abaixo.

O uso da instrução COPY dá suporte à seguinte configuração:

1. O **serviço vinculado de origem e o formato** acompanham os seguintes tipos e métodos de autenticação:

    | Tipo de armazenamento de dados de origem compatíveis                             | Formato compatível           | Tipos de autenticação de origem compatíveis                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | [Texto delimitado](format-delimited-text.md)             | Autenticação de chave de conta, autenticação de assinatura de acesso compartilhado, autenticação de entidade de serviço, autenticação de identidade gerenciada |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autenticação de chave de conta, autenticação de assinatura de acesso compartilhado |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Autenticação de chave de conta, autenticação de assinatura de acesso compartilhado |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Texto delimitado](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Autenticação de chave de conta, autenticação de entidade de serviço, autenticação de identidade gerenciada |

    >[!IMPORTANT]
    >Se o Armazenamento do Azure estiver configurado com o ponto de extremidade de serviço de VNet, você precisará usar a autenticação de identidade gerenciada – veja [Impacto de usar pontos de extremidade de serviço de VNet com Armazenamento do Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias no Data Factory das seções [Blob do Azure – autenticação de identidade gerenciada](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 – autenticação de identidade gerenciada](connector-azure-data-lake-storage.md#managed-identity), respectivamente.

2. As configurações de formato têm as seguintes particularidades:

   1. Para **Parquet**: `compression` pode ser **sem compactação**, **Snappy** ou **GZip**.
   2. Para **ORC**: `compression` pode ser **sem compactação**, **```zlib```** ou **Snappy**.
   3. Para **Texto delimitado**:
      1. `rowDelimiter` é definido explicitamente como **caractere único** ou " **\r\n**", o valor padrão não é compatível.
      2. `nullValue` é deixado como padrão ou definido como **cadeia de caracteres vazia** ("").
      3. `encodingName` é deixado como padrão ou definido como **utf-8 ou utf-16**.
      4. `escapeChar` precisa ser o mesmo que `quoteChar` e não está vazio.
      5. `skipLineCount` é deixado como padrão ou definido como 0.
      6. `compression` pode ser **nenhuma compactação** ou **GZip**.

3. Se a sua origem for uma pasta, `recursive` na atividade de cópia precisará ser definida como true e `wildcardFilename` precisará ser `*`. 

4. `wildcardFolderPath`, `wildcardFilename` (além de `*`), `modifiedDateTimeStart`, `modifiedDateTimeEnd` e `additionalColumns` não foram especificados.

As seguintes configurações da instrução COPY são compatíveis em `allowCopyCommand` na atividade de cópia:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Especifica os valores padrão para cada coluna de destino no SQL DW.  Os valores padrão na propriedade substituem a restrição DEFAULT definida no data warehouse e a coluna de identidade não pode ter um valor padrão. | Não |
| additionalOptions | Opções adicionais que serão passadas para a instrução COPY do SQL DW diretamente na cláusula "With" na [instrução COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Coloque o valor entre aspas conforme necessário para alinhar com os requisitos da instrução COPY. | Não |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true,
                "copyCommandSettings": {
                    "defaultValues": [
                        {
                            "columnName": "col_string",
                            "defaultValue": "DefaultStringValue"
                        }
                    ],
                    "additionalOptions": {
                        "MAXERRORS": "10000",
                        "DATEFORMAT": "'ymd'"
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar em tabelas do Azure Synapse Analytics. Para obter mais informações, confira [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) nos fluxos de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

Configurações específicas para o Azure Synapse Analytics estão disponíveis na guia **Opções de Origem** da transformação de origem.

**Entrada** do Selecione se você apontar sua fonte em uma tabela (equivalente de ```Select * from <table-name>``` ) ou inserir uma consulta SQL personalizada.

**Habilitar preparo** É altamente recomendável que você use essa opção em cargas de trabalho de produção com fontes Synapse DW. Quando você executa uma atividade de fluxo de dados com fontes Synapase de um pipeline, o ADF solicitará uma conta de armazenamento de local de preparo e usará isso para o carregamento de dados em etapas. É o mecanismo mais rápido para carregar dados do Synapse DW.

**Consulta**: se você selecionar Consulta no campo de entrada, insira uma consulta SQL para sua origem. Essa configuração substitui qualquer tabela que você tenha escolhido no conjunto de dados. Cláusulas **Order By** não são compatíveis aqui, mas você pode definir uma instrução SELECT FROM completa. Também pode usar funções de tabela definidas pelo usuário. **select * from udfGetData()** é um UDF no SQL que retorna uma tabela. Essa consulta produzirá uma tabela de origem que você pode usar em seu fluxo de dados. O uso de consultas também é uma ótima maneira de reduzir linhas para testes ou pesquisas.

Exemplo do SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Tamanho do lote**: insira um tamanho de lote para dividir dados grandes em leituras. Em fluxos de dados, o ADF usará essa configuração para definir o cache de coluna do Spark. Esse é um campo de opção, que usará padrões do Spark se ele for deixado em branco.

**Nível de isolamento**: o padrão para as origens SQL no fluxo de dados de mapeamento é leitura não confirmada. Você pode alterar o nível de isolamento aqui para um destes valores:

- Leitura Confirmada
- Leitura Não Confirmada
- Leitura repetida
- Serializable *-nenhum (ignorar nível de isolamento)

![Nível de Isolamento](media/data-flow/isolationlevel.png "Nível de Isolamento")

### <a name="sink-transformation"></a>Transformação de coletor

Configurações específicas para o Azure Synapse Analytics estão disponíveis na guia **Configurações** da transformação de coletor.

**Método Update:** determina quais operações são permitidas no destino do banco de dados. O padrão é permitir apenas inserções. Para atualizar, fazer upsert ou excluir linhas, uma transformação alter-row é necessária para marcar as linhas para essas ações. Para atualizações, upserts e exclusões, é necessário selecionar uma coluna de chave ou colunas para determinar qual linha alterar.

**Ação de tabela:** determina se deve-se recriar ou remover todas as linhas da tabela de destino antes da gravação.

- Nenhuma: nenhuma ação será feita na tabela.
- Recriar: a tabela será descartada e recriada. Necessário ao criar uma tabela dinamicamente.
- Truncar: todas as linhas da tabela de destino serão removidas.

**Habilitar preparo:** determina se o [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) deve ou não ser usado ao gravar no Azure Synapse Analytics

**Tamanho do lote**: controla quantas linhas estão sendo gravadas em cada bucket. Tamanhos de lote maiores aprimoram a compactação e a otimização de memória, mas geram risco de exceções de memória insuficiente ao armazenar dados em cache.

**Scripts SQL prévios e posteriores**: Insira scripts SQL de várias linhas que serão executados antes (pré-processamento) e após (pós-processamento) os dados serem gravados no banco de dados do coletor

![Scripts SQL de pré e pós-processamento](media/data-flow/prepost1.png "Scripts de processamento SQL")

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique [Atividade GetMetadata](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapeamento do tipo de dados para o Azure Synapse Analytics

Quando você copia dados de ou para o Azure Synapse Analytics, os mapeamentos a seguir são usados dos tipos de dados do Azure Synapse Analytics para os tipos de dados temporários do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o coletor.

>[!TIP]
>Confira o artigo [Tipos de dados de tabela no Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) em tipos de dados compatíveis do SQL DW e as soluções alternativas para os não compatíveis.

| Tipo de dados do Azure Synapse Analytics    | Tipo de dados provisório do Data Factory |
| :------------------------------------ | :----------------------------- |
| BIGINT                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| date                                  | Datetime                       |
| Datetime                              | Datetime                       |
| datetime2                             | Datetime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| INT                                   | Int32                          |
| money                                 | Decimal                        |
| NCHAR                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| NVARCHAR                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Datetime                       |
| SMALLINT                              | Int16                          |
| SMALLMONEY                            | Decimal                        |
| time                                  | TimeSpan                       |
| TINYINT                               | Byte                           |
| UNIQUEIDENTIFIER                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de repositórios de dados com suporte como fontes e repositórios por Atividade de Cópia no Azure Data Factory, consulte [ repositórios de dados e formatos suportados ](copy-activity-overview.md#supported-data-stores-and-formats).
