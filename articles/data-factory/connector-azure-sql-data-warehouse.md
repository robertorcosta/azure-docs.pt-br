---
title: Copiar e transformar dados no Azure Synapse Analytics
description: Saiba como copiar dados de e para a análise de Synapse do Azure e transformar dados na análise de Synapse do Azure usando Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: c23bba777a7a29f572764914cfa505d2b668683d
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830404"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar e transformar dados no Azure Synapse Analytics (anteriormente SQL Data Warehouse do Azure) usando Azure Data Factory 

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão atual](connector-azure-sql-data-warehouse.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para a análise de Synapse do Azure e usar o fluxo de dados para transformar dados em Azure Data Lake Storage Gen2. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Há suporte para este conector do Azure Synapse Analytics para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com tabela de [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Para a atividade de cópia, este conector do Azure Synapse Analytics dá suporte a essas funções:

- Copie os dados usando a autenticação de token do Aplicativo Azure AD (Azure Active Directory) e autenticação do SQL com uma entidade de serviço ou identidades gerenciadas para recursos do Azure.
- Como uma fonte, recupere dados usando uma consulta SQL ou procedimento armazenado.
- Como um coletor, carregue dados usando o [polybase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) ou a [instrução de cópia](#use-copy-statement) (visualização) ou BULK INSERT. Recomendamos o polybase ou a instrução de cópia (versão prévia) para melhor desempenho de cópia.

> [!IMPORTANT]
> Se você copiar dados usando o Integration Runtime do Azure Data Factory, configure um [firewall do servidor SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os serviços do Azure possam acessar o servidor.
> Se você copiar dados usando um runtime de integração auto-hospedada, configure o firewall do servidor SQL do Azure para permitir o intervalo de IP apropriado. Esse intervalo inclui o IP do computador que é usado para se conectar ao Azure Synapse Analytics.

## <a name="get-started"></a>Comece agora

> [!TIP]
> Para obter o melhor desempenho, use o polybase para carregar dados no Azure Synapse Analytics. A seção [usar o polybase para carregar dados na análise de Synapse do Azure](#use-polybase-to-load-data-into-azure-sql-data-warehouse) tem detalhes. Para obter instruções com um caso de uso, confira [carregar 1 TB no Azure Synapse Analytics em 15 minutos com Azure data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que definem Data Factory entidades específicas para um conector do Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para um serviço vinculado do Azure Synapse Analytics:

| Propriedade            | Description                                                  | Obrigatório                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | A propriedade type deve ser definida como  **AzureSqlDW**.             | Sim                                                          |
| connectionString    | Especifique as informações necessárias para se conectar à instância do Azure Synapse Analytics para a propriedade **ConnectionString** . <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar uma senha/chave da entidade de serviço no Azure Key Vault e se sua autenticação do SQL efetua pull da configuração da `password` da cadeia de conexão. Veja o exemplo de JSON abaixo da tabela e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) que fornece mais detalhes. | Sim                                                          |
| servicePrincipalId  | Especifique a ID do cliente do aplicativo.                         | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| locatário              | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-lo focalizando o canto superior direito do portal do Azure. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| connectVia          | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Azure Integration runtime ou um runtime de integração auto-hospedada (se o seu armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não                                                           |

Para diferentes tipos de autenticação, consulte as seções a seguir sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- Autenticação de token de aplicativo do Azure AD: [Principal de serviço](#service-principal-authentication)
- Autenticação de token do aplicativo Azure AD: [Identidades gerenciadas para recursos do Azure](#managed-identity)

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

1. **[Crie um aplicativo do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** no portal do Azure. Anote o nome do aplicativo e os seguintes valores que definem o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. **[Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o servidor do SQL Azure no portal do Azure, caso ainda não tenha feito isso. O administrador do Azure AD pode ser um usuário ou um grupo do Azure AD. Se você conceder uma função de administrador ao grupo com a identidade gerenciada, ignore as etapas 3 e 4. O administrador terá acesso completo ao banco de dados.

3. **[ Crie usuários de banco de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para o diretor de serviços. Conecte-se ao data warehouse de ou para o qual você deseja copiar dados usando ferramentas como SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o seguinte T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceda ao principal de serviço as permissões necessárias**, como faria normalmente para usuários do SQL ou outros. Execute o código a seguir ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se você quiser usar o polybase para carregar os dados, aprenda a [permissão de banco](#required-database-permission)de dado necessária.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configure um serviço vinculado do Azure Synapse Analytics** no Azure data Factory.


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

### <a name="managed-identity"></a> Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa o factory específico. Você pode usar essa identidade gerenciada para a autenticação do Azure Synapse Analytics. A fábrica designada pode acessar e copiar dados de ou para seu data warehouse usando essa identidade.

Para usar a autenticação de identidade gerenciada, siga estas etapas:

1. **[Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o servidor do SQL Azure no portal do Azure, caso ainda não tenha feito isso. O administrador do Azure AD pode ser um usuário ou um grupo do Azure AD. Se você conceder uma função de administrador ao grupo com a identidade gerenciada, ignore as etapas 3 e 4. O administrador terá acesso completo ao banco de dados.

2. **[Crie usuários de banco de dados independente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para a identidade gerenciada data Factory. Conecte-se ao data warehouse de ou para o qual você deseja copiar dados usando ferramentas como SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o seguinte T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Conceda à identidade gerenciada data Factory permissões necessárias** como faria normalmente para usuários do SQL e outros. Execute o código a seguir ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se você quiser usar o polybase para carregar os dados, aprenda a [permissão de banco](#required-database-permission)de dado necessária.

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Configure um serviço vinculado do Azure Synapse Analytics** no Azure data Factory.

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

As propriedades a seguir têm suporte para o conjunto de porta do Azure Synapse Analytics:

| Propriedade  | Description                                                  | Obrigatório                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | A propriedade **tipo** do conjunto de dados deve ser definida como  **AzureSqlDWTable**. | Sim                         |
| esquema | Nome do esquema. |Não para fonte, Sim para o coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, Sim para o coletor  |
| tableName | Nome da tabela/exibição com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Para uma nova carga de trabalho, use `schema` e `table`. | Não para fonte, Sim para o coletor |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades do conjunto de dados

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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics como a origem

Para copiar dados do Azure Synapse Analytics, defina a propriedade **Type** na origem da atividade de cópia como **SqlDWSource**. As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade                     | Description                                                  | Obrigatório |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | A propriedade **tipo** da origem da Atividade de Cópia deve ser configurada para **SqlDWSource**. | Sim      |
| sqlReaderQuery               | Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. | Não       |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. | Não       |
| storedProcedureParameters    | Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não       |

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Análise de Synapse do Azure como coletor

O Azure Data Factory dá suporte a três maneiras de carregar dados em SQL Data Warehouse.

![Opções de cópia do coletor do SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Usar polybase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Usar instrução de cópia (visualização)](#use-copy-statement)
- Usar inserção em massa

A maneira mais rápida e escalonável de carregar dados é por meio do [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) ou da [instrução de cópia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (versão prévia).

Para copiar dados para o SQL Data Warehouse do Azure, defina o tipo de coletor em Atividade de Cópia para **SqlDWSink**. As seguintes propriedades são suportadas na seção Copy Activity **sink**:

| Propriedade          | Description                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | A propriedade **tipo** do coletor de Atividade de Cópia deve ser definida como **SqlDWSink**. | Sim                                           |
| allowPolyBase     | Indica se o polybase deve ser usado para carregar dados em SQL Data Warehouse. `allowCopyCommand` e `allowPolyBase` não podem ser ambos verdadeiros. <br/><br/>Confira a seção [Usar o PolyBase para carregar dados no Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter os detalhes e as restrições.<br/><br/>Os valores permitidos são **True** e **False** (padrão). | Não.<br/>Aplicar ao usar o polybase.     |
| polyBaseSettings  | Um grupo de propriedades que pode ser especificado quando a propriedade `allowPolybase` é definida como **true**. | Não.<br/>Aplicar ao usar o polybase. |
| allowCopyCommand | Indica se a [instrução de cópia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (versão prévia) deve ser usada para carregar dados em SQL data warehouse. `allowCopyCommand` e `allowPolyBase` não podem ser ambos verdadeiros. <br/><br/>Confira [a seção usar a instrução Copy para carregar dados no Azure SQL data warehouse](#use-copy-statement) para obter restrições e detalhes.<br/><br/>Os valores permitidos são **True** e **False** (padrão). | Não.<br>Aplicar ao usar copiar. |
| copyCommandSettings | Um grupo de propriedades que pode ser especificado quando `allowCopyCommand` propriedade é definida como TRUE. | Não.<br/>Aplicar ao usar copiar. |
| writeBatchSize    | Número de linhas a serem inseridas na tabela SQL **por lote**.<br/><br/>O valor permitido é **inteiro** (número de linhas). Por padrão, Data Factory determina dinamicamente o tamanho do lote apropriado com base no tamanho da linha. | Não.<br/>Aplicar ao usar inserção em massa.     |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lote ser concluída antes de expirar.<br/><br/>O valor permitido é **timespan**. Exemplo: "00:30:00" (30 minutos). | Não.<br/>Aplicar ao usar inserção em massa.        |
| preCopyScript     | Especifique uma consulta SQL para que a Atividade de Cópia seja executada antes de gravar dados no Azure SQL Data Warehouse em cada execução. Use essa propriedade para limpar os dados pré-carregados. | Não                                            |
| tableOption | Especifica se a tabela do coletor deve ser criada automaticamente se não existir com base no esquema de origem. Não há suporte para a criação de tabela automática quando a cópia preparada está configurada na atividade de cópia. Os valores permitidos são: `none` (padrão), `autoCreate`. |Não |
| disableMetricsCollection | Data Factory coleta métricas como SQL Data Warehouse DWUs para otimização e recomendações de desempenho de cópia. Se você estiver preocupado com esse comportamento, especifique `true` para desativá-lo. | Não (o padrão é `false`) |

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

Usar o [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) é uma maneira eficiente de carregar uma grande quantidade de dados no Azure Synapse Analytics com alta taxa de transferência. Você verá um grande ganho na taxa de transferência usando PolyBase em vez do mecanismo BULKINSERT padrão. Para obter instruções com um caso de uso, consulte [carregar 1 TB na análise de Synapse do Azure](v1/data-factory-load-sql-data-warehouse.md).

* Se os dados de origem estiverem no **blob do Azure, Azure data Lake Storage Gen1 ou Azure data Lake Storage Gen2**e o **formato for compatível com o polybase**, você poderá usar a atividade de cópia para invocar diretamente o polybase para permitir que o Azure SQL data warehouse receba os dados da origem. Para detalhes, veja **[ Cópia direta usando PolyBase ](#direct-copy-by-using-polybase)** .
* Se o armazenamento e o formato de dados de origem não forem originalmente suportados pelo PolyBase, use a **[cópia Staged usando o recurso PolyBase ](#staged-copy-by-using-polybase)** . O recurso de cópia preparada também oferece melhor rendimento. Ele converte automaticamente os dados em formato compatível com o polybase, armazena os dados no armazenamento de BLOBs do Azure. em seguida, chama o polybase para carregar dados em SQL Data Warehouse.

>[!TIP]
>Saiba mais sobre [as práticas recomendadas para usar o polybase](#best-practices-for-using-polybase).

As seguintes configurações do polybase têm suporte em `polyBaseSettings` na atividade de cópia:

| Propriedade          | Description                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Especifica o número ou o percentual de linhas que podem ser rejeitadas antes de a consulta falhar.<br/><br/>Saiba mais sobre as opções de rejeição do PolyBase na seção Argumentos de [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Os valores permitidos são 0 (padrão), 1, 2 etc. | Não                                            |
| rejectType        | Especifica se a opção **rejectValue** é um valor literal ou uma porcentagem.<br/><br/>Os valores permitidos são **Valor** (padrão) e **Porcentagem**. | Não                                            |
| rejectSampleValue | Determina o número de linhas a serem recuperadas antes que o PolyBase recalcule a porcentagem de linhas rejeitadas.<br/><br/>Os valores permitidos são 1, 2 etc. | Sim, se o **rejectType** for  **porcentagem**. |
| useTypeDefault    | Especifica como tratar valores ausentes em arquivos de texto delimitados quando o PolyBase recuperar dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção Argumentos em [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Os valores permitidos são **True** e **False** (padrão).<br><br> | Não                                            |

### <a name="direct-copy-by-using-polybase"></a>Cópia direta usando PolyBase

SQL Data Warehouse o polybase oferece suporte direto ao blob do Azure, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Se os dados de origem atenderem aos critérios descritos nesta seção, use o polybase para copiar diretamente do armazenamento de dados de origem para o Azure Synapse Analytics. Caso contrário, use [cópia Staged usando PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Para copiar dados com eficiência para SQL Data Warehouse, saiba mais em [Azure data Factory torna ainda mais fácil e conveniente descobrir informações de dados ao usar data Lake Store com SQL data warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem atendidos, o Azure Data Factory verificará as configurações e retornará automaticamente ao mecanismo BULKINSERT para a movimentação de dados.

1. O **serviço vinculado de origem** é com os seguintes tipos e métodos de autenticação:

    | Tipo de armazenamento de dados de origem com suporte                             | Tipo de autenticação de origem com suporte                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | Autenticação de chave de conta, autenticação de identidade gerenciada |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autenticação de entidade de serviço                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autenticação de chave de conta, autenticação de identidade gerenciada |

    >[!IMPORTANT]
    >Se o armazenamento do Azure estiver configurado com o ponto de extremidade de serviço de VNet, você deverá usar a autenticação de identidade gerenciada-consulte o [impacto de usar pontos de extremidade de serviço de vnet com o armazenamento do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias em Data Factory da seção [autenticação de identidade gerenciada pelo blob do Azure](connector-azure-blob-storage.md#managed-identity) e [autenticação de identidade gerenciada por Azure data Lake Storage Gen2,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.

2. O **formato de dados de origem** é de **parquet**, **Orc**ou **texto delimitado**, com as seguintes configurações:

   1. O caminho da pasta não contém o filtro curinga.
   2. O nome do arquivo está vazio ou aponta para um único arquivo. Se você especificar o nome de arquivo curinga na atividade de cópia, ele só poderá ser `*` ou `*.*`.
   3. `rowDelimiter` é **Default**, **\n**, **\r\n**ou **\r**.
   4. `nullValue` é deixado como padrão ou definida como **cadeia de caracteres vazia** (""), e `treatEmptyAsNull` é deixado como padrão ou definido como true.
   5. `encodingName` é deixado como padrão ou definido como **UTF-8**.
   6. `quoteChar`, `escapeChar`e `skipLineCount` não estão especificados. O polybase oferece suporte à linha de cabeçalho Skip, que pode ser configurada como `firstRowAsHeader` no ADF.
   7. `compression` pode ser **sem compactação**, **GZip** ou **Deflate**.

3. Se sua origem for uma pasta, `recursive` na atividade de cópia deverá ser definida como true.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`e `modifiedDateTimeEnd` não são especificados.

>[!NOTE]
>Se sua origem for uma pasta, o polybase recuperará arquivos da pasta e de todas as suas subpastas e não recuperará dados de arquivos para os quais o nome do arquivo começa com um sublinhado (_) ou um ponto final (.), conforme documentado [aqui – argumento de local](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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

Quando os dados de origem não são nativamente compatíveis com o polybase, habilite a cópia de dados por meio de uma instância de armazenamento de BLOBs do Azure de preparo provisório (não pode ser o armazenamento Premium do Azure). Nesse caso, Azure Data Factory converte automaticamente os dados para atender aos requisitos de formato de dados do polybase. Em seguida, ele invoca o polybase para carregar dados em SQL Data Warehouse. Finalmente, ele limpa seus dados temporários do armazenamento de blobs. Consulte [Cópia preparada](copy-activity-performance.md#staged-copy) para obter detalhes sobre a cópia de dados por meio de uma instância de armazenamento de Blob do Azure de preparação.

Para usar esse recurso, crie um [serviço vinculado do armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#linked-service-properties) que se refere à conta de armazenamento do Azure com o armazenamento de BLOBs provisório. Em seguida, especifique as propriedades `enableStaging` e `stagingSettings` para a atividade de cópia, conforme mostrado no código a seguir.

>[!IMPORTANT]
>Se o armazenamento do Azure de preparo estiver configurado com o ponto de extremidade do serviço VNet, você deverá usar a autenticação de identidade gerenciada-consulte o [impacto de usar pontos de extremidade do serviço vnet com o armazenamento do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias em Data Factory da [autenticação de identidade gerenciada pelo blob do Azure](connector-azure-blob-storage.md#managed-identity).

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

As seções a seguir fornecem as práticas recomendadas além das mencionadas nas [práticas recomendadas para o Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

#### <a name="required-database-permission"></a>Permissão de banco de dados obrigatória

Para usar o PolyBase, o usuário que carrega dados no SQL Data Warehouse deve ter a [permissão "CONTROLE"](https://msdn.microsoft.com/library/ms191291.aspx) no banco de dados de destino. Uma maneira de conseguir isso é adicionar o usuário como membro da função **db_owner**. Aprenda como fazer isso na [visão geral do SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Limites de tamanho de linha e tipo de dados

As cargas do PolyBase são limitadas a linhas menores que 1 MB. Ele não pode ser usado para carregar para VARCHR (MAX), NVARCHAR (MAX) ou VARBINARY (MAX). Para obter mais informações, consulte [Limites de capacidade do serviço SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando os dados de origem tiverem linhas maiores que 1 MB, talvez você queira dividir verticalmente as tabelas de origem em várias pequenas. Certifique-se de que o maior tamanho de cada linha não exceda o limite. As tabelas menores podem então ser carregadas usando o polybase e mescladas em conjunto no Azure Synapse Analytics.

Como alternativa, para dados com colunas largas, você pode usar não polybase para carregar os dados usando o ADF, desativando a configuração "permitir polybase".

#### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse

Para obter a melhor taxa de transferência possível, atribua uma classe de recurso maior para o usuário que carrega dados em SQL Data Warehouse por meio do PolyBase.

#### <a name="polybase-troubleshooting"></a>Solucionando problemas do PolyBase

**Carregando na coluna decimal**

Se os dados de origem estiverem em formato de texto ou em outros repositórios compatíveis com polybase (usando cópia em etapas e polybase) e contiverem um valor vazio a ser carregado em SQL Data Warehouse coluna decimal, você poderá encontrar o seguinte erro:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A solução é desmarcar a opção "**usar o padrão do tipo**" (como false) nas configurações do polybase do coletor da atividade de cópia->. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" é uma configuração nativa do polybase, que especifica como tratar valores ausentes em arquivos de texto delimitados quando o polybase recupera dados do arquivo de texto. 

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

O valor NULL é uma forma especial do valor padrão. Se a coluna permite valor nulo, os dados de entrada no blob para essa coluna podem estar vazios. Mas não pode estar ausente do conjunto de dados de entrada. O polybase insere nulo para valores ausentes no Azure Synapse Analytics.

## <a name="use-copy-statement"></a>Usar a instrução de cópia para carregar dados no Azure SQL Data Warehouse (versão prévia)

SQL Data Warehouse [instrução de cópia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (versão prévia) dá suporte diretamente ao carregamento de dados do **blob do Azure e Azure data Lake Storage Gen2**. Se os dados de origem atendem aos critérios descritos nesta seção, você pode optar por usar a instrução de cópia no ADF para carregar dados no Azure SQL Data Warehouse. Azure Data Factory verifica as configurações e falha na execução da atividade de cópia se os critérios não forem atendidos.

>[!NOTE]
>Atualmente Data Factory só oferece suporte à cópia de fontes compatíveis de instruções de cópia mencionadas abaixo.

O uso da instrução COPY dá suporte à seguinte configuração:

1. O **formato e o serviço vinculado de origem** são os seguintes tipos e métodos de autenticação:

    | Tipo de armazenamento de dados de origem com suporte                             | Formato com suporte           | Tipo de autenticação de origem com suporte                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | [Texto delimitado](format-delimited-text.md)             | Autenticação de chave de conta, autenticação de assinatura de acesso compartilhado, autenticação de entidade de serviço, autenticação de identidade gerenciada |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autenticação de chave de conta, autenticação de assinatura de acesso compartilhado |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Autenticação de chave de conta, autenticação de assinatura de acesso compartilhado |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Texto delimitado](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Autenticação de chave de conta, autenticação de entidade de serviço, autenticação de identidade gerenciada |

    >[!IMPORTANT]
    >Se o armazenamento do Azure estiver configurado com o ponto de extremidade de serviço de VNet, você deverá usar a autenticação de identidade gerenciada-consulte o [impacto de usar pontos de extremidade de serviço de vnet com o armazenamento do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias em Data Factory da seção [autenticação de identidade gerenciada pelo blob do Azure](connector-azure-blob-storage.md#managed-identity) e [autenticação de identidade gerenciada por Azure data Lake Storage Gen2,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.

2. As configurações de formato são as seguintes:

   1. Para **parquet**: `compression` não pode ser **nenhuma compactação**, **ajuste**ou **gzip**.
   2. Para **Orc**: `compression` pode **não ser compactação**, **zlib**ou **snapshot**.
   3. Para **texto delimitado**:
      1. `rowDelimiter` é definido explicitamente como **um caractere único** ou " **\r\n**", não há suporte para o valor padrão.
      2. `nullValue` é deixado como padrão ou definido como uma **cadeia de caracteres vazia** ("").
      3. `encodingName` é deixado como padrão ou definida como **UTF-8 ou UTF-16**.
      4. `escapeChar` deve ser igual a `quoteChar`e não está vazia.
      5. `skipLineCount` é deixado como padrão ou definido como 0.
      6. `compression` pode ser **sem compactação** ou **gzip**.

3. Se sua origem for uma pasta, `recursive` na atividade de cópia deverá ser definida como true.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`e `modifiedDateTimeEnd` não são especificados.

As seguintes configurações de instrução de cópia têm suporte em `allowCopyCommand` na atividade de cópia:

| Propriedade          | Description                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| valores de | Especifica os valores padrão para cada coluna de destino no SQL DW.  Os valores padrão na propriedade substituem a restrição padrão definida no data warehouse, e a coluna de identidade não pode ter um valor padrão. | Não |
| additionalOptions | Opções adicionais que serão passadas para a instrução de cópia do SQL DW diretamente na cláusula "with" na [instrução Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Citar o valor conforme necessário para alinhar com os requisitos da instrução de cópia. | Não |

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


## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo de dados do SQL Data Warehouse do Azure

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar em tabelas do Azure Synapse Analytics. Para obter mais informações, consulte a [transformação origem](data-flow-source.md) e a [transformação coletor](data-flow-sink.md) no mapeamento de fluxos de dados.

### <a name="source-transformation"></a>Transformação de origem

As configurações específicas para o Azure Synapse Analytics estão disponíveis na guia **Opções de origem** da transformação origem. 

**Entrada:** Selecione se você apontar sua fonte em uma tabela (equivalente a ```Select * from <table-name>```) ou inserir uma consulta SQL personalizada.

**Consulta**: se você selecionar consulta no campo de entrada, insira uma consulta SQL para sua origem. Essa configuração substitui qualquer tabela que você tenha escolhido no conjunto de um. Não há suporte para cláusulas **ordenar por** aqui, mas você pode definir uma instrução SELECT FROM completa. Você também pode usar funções de tabela definidas pelo usuário. **Select * de udfGetData ()** é um UDF no SQL que retorna uma tabela. Essa consulta produzirá uma tabela de origem que você pode usar em seu fluxo de dados. O uso de consultas também é uma ótima maneira de reduzir linhas para teste ou pesquisas. 

* Exemplo de SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Tamanho do lote**: Insira um tamanho de lote para dividir dados grandes em leituras.

**Nível de isolamento**: o padrão para as fontes SQL no fluxo de dados de mapeamento é leitura não confirmada. Você pode alterar o nível de isolamento aqui para um destes valores:
* Leitura confirmada
* Leitura não confirmada
* Leitura repetida
* Serializável
* Nenhum (ignorar nível de isolamento)

![Nível de isolamento](media/data-flow/isolationlevel.png "Nível de Isolamento")

### <a name="sink-transformation"></a>Transformação do coletor

As configurações específicas para o Azure Synapse Analytics estão disponíveis na guia **configurações** da transformação coletor.

**Método de atualização:** Determina quais operações são permitidas no destino do banco de dados. O padrão é permitir apenas inserções. Para atualizar, upsertr ou excluir linhas, uma transformação ALTER-Row é necessária para marcar linhas para essas ações. Para atualizações, upserts e exclusões, uma coluna ou colunas de chave deve ser definida para determinar qual linha alterar.

**Ação da tabela:** Determina se deve-se recriar ou remover todas as linhas da tabela de destino antes da gravação.
* Nenhum: nenhuma ação será feita para a tabela.
* Recriar: a tabela será descartada e recriada. Necessário se estiver criando uma nova tabela dinamicamente.
* Truncar: todas as linhas da tabela de destino serão removidas.

**Habilitar preparo:** Determina se o [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) deve ou não ser usado ao gravar no Azure Synapse Analytics

**Tamanho do lote**: controla quantas linhas estão sendo gravadas em cada Bucket. Tamanhos de lote maiores melhoram a compactação e a otimização de memória, mas não têm risco de exceções de memória ao armazenar dados em cache.

**Scripts SQL anteriores e posteriores**: Insira os scripts SQL de várias linhas que serão executados antes (pré-processamento) e após (pós-processamento) os dados são gravados no banco de dado do coletor

![pré e pós-scripts de processamento do SQL](media/data-flow/prepost1.png "Scripts de processamento SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapeamento de tipo de dados para o Azure Synapse Analytics

Quando você copia dados de ou para o Azure Synapse Analytics, os seguintes mapeamentos são usados de tipos de dados do Azure Synapse Analytics para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o coletor.

>[!TIP]
>Consulte o artigo [tipos de dados de tabela no Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) em tipos de dados com suporte do SQL DW e as soluções alternativas para os não suportados.

| Tipo de dados do Azure Synapse Analytics    | Tipo de dados provisório do Data Factory |
| :------------------------------------ | :----------------------------- |
| BIGINT                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| NCHAR                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| NVARCHAR                              | String, Char[]                 |
| real                                  | Individual                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| SMALLINT                              | Int16                          |
| SMALLMONEY                            | Decimal                        |
| time                                  | TimeSpan                       |
| TINYINT                               | Byte                           |
| UNIQUEIDENTIFIER                      | GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Próximos passos
Para obter uma lista de repositórios de dados com suporte como fontes e repositórios por Atividade de Cópia no Azure Data Factory, consulte [ repositórios de dados e formatos suportados ](copy-activity-overview.md##supported-data-stores-and-formats).
