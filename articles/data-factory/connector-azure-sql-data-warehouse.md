---
title: Copiar e transformar dados no Azure Synapse Analytics
description: Aprenda a copiar dados de e para o Azure Synapse Analytics e transforme dados no Azure Synapse Analytics usando a Fábrica de Dados.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 7fb1560fb9be809d816dde7dd69f1ec8afe5649f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417571"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar e transformar dados no Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse) usando o Azure Data Factory 

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão atual](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados do Azure para copiar dados e para o Azure Synapse Analytics e usar o Fluxo de Dados para transformar dados no Azure Data Lake Storage Gen2. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados do Azure para copiar dados de e para o Azure SQL Data Warehouse e usar o Fluxo de Dados para transformar dados no Azure Data Lake Storage Gen2. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados do Azure para copiar dados e para o Azure Synapse Analytics e usar o Fluxo de Dados para transformar dados no Azure Data Lake Storage Gen2. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Azure Synapse Analytics é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com tabela [de matriz de origem/pia suportada](copy-activity-overview.md)
- [Mapeamento do fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Para a atividade do Copy, este conector Azure Synapse Analytics suporta essas funções:

- Copie os dados usando a autenticação de token do Aplicativo Azure AD (Azure Active Directory) e autenticação do SQL com uma entidade de serviço ou identidades gerenciadas para recursos do Azure.
- Como uma fonte, recupere dados usando uma consulta SQL ou procedimento armazenado.
- Como um dissipador, carregue dados usando a [instrução](#use-copy-statement) [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) ou COPY (visualização) ou inserção em massa. Recomendamos a declaração PolyBase ou COPY (visualização) para melhor desempenho da cópia.

> [!IMPORTANT]
> Se você copiar dados usando o Integration Runtime do Azure Data Factory, configure um [firewall do servidor SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os serviços do Azure possam acessar o servidor.
> Se você copiar dados usando um runtime de integração auto-hospedada, configure o firewall do servidor SQL do Azure para permitir o intervalo de IP apropriado. Esta gama inclui o IP da máquina que é usado para se conectar ao Azure Synapse Analytics.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter o melhor desempenho, use o PolyBase para carregar dados no Azure Synapse Analytics. O use polyBase para carregar dados na seção [Azure Synapse Analytics tem detalhes.](#use-polybase-to-load-data-into-azure-sql-data-warehouse) Para obter um passo a passo com um estojo de uso, consulte [Load 1 TB no Azure Synapse Analytics em menos de 15 minutos com o Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que definem entidades da Fábrica de Dados específicas para um conector Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para um serviço vinculado ao Azure Synapse Analytics:

| Propriedade            | Descrição                                                  | Obrigatório                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | A propriedade type deve ser definida como ** AzureSqlDW**.             | Sim                                                          |
| connectionString    | Especifique as informações necessárias para se conectar à instância do Azure Synapse Analytics para a propriedade **connectionString.** <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar uma senha/chave da entidade de serviço no Azure Key Vault e se sua autenticação do SQL efetua pull da configuração da `password` da cadeia de conexão. Veja o exemplo de JSON abaixo da tabela e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) que fornece mais detalhes. | Sim                                                          |
| servicePrincipalId  | Especifique a ID do cliente do aplicativo.                         | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| locatário              | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-lo focalizando o canto superior direito do portal do Azure. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| connectVia          | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Azure Integration runtime ou um runtime de integração auto-hospedada (se o seu armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não                                                           |

Para diferentes tipos de autenticação, consulte as seções a seguir sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação SQL](#sql-authentication)
- Autenticação de token de aplicativo do Azure AD: [Principal de serviço](#service-principal-authentication)
- Autenticação de token de aplicativo Azure AD: [identidades gerenciadas para recursos do Azure](#managed-identity)

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

4. **Conceda ao principal de serviço as permissões necessárias**, como faria normalmente para usuários do SQL ou outros. Execute o seguinte código ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se você quiser usar o PolyBase para carregar os dados, aprenda a [permissão de banco de dados necessária](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configure um serviço vinculado ao Azure Synapse Analytics** na Fábrica de Dados Azure.


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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa o factory específico. Você pode usar essa identidade gerenciada para autenticação do Azure Synapse Analytics. A fábrica designada pode acessar e copiar dados de ou para seu data warehouse usando essa identidade.

Para usar a autenticação de identidade gerenciada, siga estas etapas:

1. **[Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o servidor do SQL Azure no portal do Azure, caso ainda não tenha feito isso. O administrador do Azure AD pode ser um usuário ou um grupo do Azure AD. Se você conceder uma função de administrador ao grupo com a identidade gerenciada, ignore as etapas 3 e 4. O administrador terá acesso completo ao banco de dados.

2. **[Criar usuários de banco de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para a Identidade Gerenciada da Fábrica de Dados. Conecte-se ao data warehouse de ou para o qual você deseja copiar dados usando ferramentas como SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o seguinte T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Conceda à Fábrica de Dados as permissões necessárias** de identidade necessária, como você normalmente faz para usuários de SQL e outros. Execute o seguinte código ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se você quiser usar o PolyBase para carregar os dados, aprenda a [permissão de banco de dados necessária](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Configure um serviço vinculado ao Azure Synapse Analytics** na Fábrica de Dados Azure.

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

As seguintes propriedades são suportadas para o conjunto de dados Azure Synapse Analytics:

| Propriedade  | Descrição                                                  | Obrigatório                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | A propriedade **tipo** do conjunto de dados deve ser definida como ** AzureSqlDWTable**. | Sim                         |
| esquema | Nome do esquema. |Não para fonte, Sim para o coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, Sim para o coletor  |
| tableName | Nome da tabela/exibição com esquema. Esta propriedade é suportada para compatibilidade retrógrada. Para nova carga `schema` `table`de trabalho, use e . | Não para fonte, Sim para o coletor |

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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte e sink do Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics como fonte

Para copiar dados do Azure Synapse Analytics, defina a propriedade **do tipo** na fonte Copy Activity como **SqlDWSource**. As seguintes propriedades são suportadas na seção Origem atividade **de** cópia:

| Propriedade                     | Descrição                                                  | Obrigatório |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | A propriedade **tipo** da origem da Atividade de Cópia deve ser configurada para **SqlDWSource**. | Sim      |
| sqlReaderQuery               | Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. | Não       |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. | Não       |
| storedProcedureParameters    | Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não       |
| isolationLevel | Especifica o comportamento de bloqueio de transações para a fonte SQL. Os valores permitidos são: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para obter mais detalhes. | Não |

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

**Procedimento armazenado em amostra:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics como pia

A Fábrica de Dados do Azure suporta três maneiras de carregar dados no SQL Data Warehouse.

![Opções de cópia do dissipador SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Use PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Use a declaração COPY (visualização)](#use-copy-statement)
- Use inserção a granel

A maneira mais rápida e escalável de carregar dados é através [do PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) ou da [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (visualização).

Para copiar dados para o SQL Data Warehouse do Azure, defina o tipo de coletor em Atividade de Cópia para **SqlDWSink**. As seguintes propriedades são suportadas na seção Copiar atividade **de ração:**

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | A propriedade **tipo** do coletor de Atividade de Cópia deve ser definida como **SqlDWSink**. | Sim                                           |
| allowPolyBase     | Indica se deve usar o PolyBase para carregar dados no SQL Data Warehouse. `allowCopyCommand`e `allowPolyBase` não pode ser verdade. <br/><br/>Confira a seção [Usar o PolyBase para carregar dados no Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter os detalhes e as restrições.<br/><br/>Os valores permitidos são **True** e **False** (padrão). | Não.<br/>Aplicar ao usar o PolyBase.     |
| polyBaseSettings  | Um grupo de propriedades que podem `allowPolybase` ser especificadas quando a propriedade é definida como **verdadeira**. | Não.<br/>Aplicar ao usar o PolyBase. |
| permitirComando de cópia | Indica se deve usar a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (visualização) para carregar dados no SQL Data Warehouse. `allowCopyCommand`e `allowPolyBase` não pode ser verdade. <br/><br/>Consulte Use a declaração COPY para carregar dados na seção [Azure SQL Data Warehouse](#use-copy-statement) para obter restrições e detalhes.<br/><br/>Os valores permitidos são **True** e **False** (padrão). | Não.<br>Aplique ao usar COPY. |
| copiarConfigurações de comando | Um grupo de propriedades que `allowCopyCommand` podem ser especificadas quando a propriedade é definida como TRUE. | Não.<br/>Aplique ao usar COPY. |
| writeBatchSize    | Número de linhas a serem inseridas na tabela SQL **por lote**.<br/><br/>O valor permitido é **inteiro** (número de linhas). Por padrão, a Fábrica de Dados determina dinamicamente o tamanho do lote apropriado com base no tamanho da linha. | Não.<br/>Aplique ao usar a inserção a granel.     |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lote ser concluída antes de expirar.<br/><br/>O valor permitido é **timespan**. Exemplo: "00:30:00" (30 minutos). | Não.<br/>Aplique ao usar a inserção a granel.        |
| preCopyScript     | Especifique uma consulta SQL para que a Atividade de Cópia seja executada antes de gravar dados no Azure SQL Data Warehouse em cada execução. Use essa propriedade para limpar os dados pré-carregados. | Não                                            |
| Tableoption | Especifica se criará automaticamente a tabela de sumidouros se não existir com base no esquema de origem. A criação da tabela automática não é suportada quando a cópia encenada é configurada na atividade de cópia. Os valores `none` permitidos `autoCreate`são: (padrão), . |Não |
| desativaçãoMetricsCollection | A Data Factory coleta métricas como DWUs do SQL Data Warehouse para otimização e recomendações de desempenho de cópia. Se você está preocupado com `true` esse comportamento, especifique para desligá-lo. | Não (o padrão é `false`) |

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

O uso [do PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) é uma maneira eficiente de carregar uma grande quantidade de dados no Azure Synapse Analytics com alto throughput. Você verá um grande ganho na taxa de transferência usando PolyBase em vez do mecanismo BULKINSERT padrão. Para obter um passo a passo com um estojo de uso, consulte [Load 1 TB no Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Se os dados de origem estão no **Azure Blob, No Azure Data Lake Storage Gen1 ou no Azure Data Lake Storage Gen2**, e o **formato for compatível com polyBase,** você pode usar a atividade de cópia para invocar diretamente o PolyBase para permitir que o Azure SQL Data Warehouse retire os dados da origem. Para detalhes, veja **[ Cópia direta usando PolyBase ](#direct-copy-by-using-polybase)**.
* Se o armazenamento e o formato de dados de origem não forem originalmente suportados pelo PolyBase, use a **[cópia Staged usando o recurso PolyBase ](#staged-copy-by-using-polybase)**. O recurso de cópia preparada também oferece melhor rendimento. Ele converte automaticamente os dados em formato compatível com PolyBase, armazena os dados no armazenamento Azure Blob.e, em seguida, chama o PolyBase para carregar dados no SQL Data Warehouse.

>[!TIP]
>Saiba mais sobre [as melhores práticas para usar o PolyBase](#best-practices-for-using-polybase).

As seguintes configurações do `polyBaseSettings` PolyBase são suportadas em atividade de cópia:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Especifica o número ou o percentual de linhas que podem ser rejeitadas antes de a consulta falhar.<br/><br/>Saiba mais sobre as opções de rejeição do PolyBase na seção Argumentos da [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Os valores permitidos são 0 (padrão), 1, 2 etc. | Não                                            |
| rejectType        | Especifica se a opção **rejectValue** é um valor literal ou uma porcentagem.<br/><br/>Os valores permitidos são **Valor** (padrão) e **Porcentagem**. | Não                                            |
| rejectSampleValue | Determina o número de linhas a serem recuperadas antes que o PolyBase recalcule a porcentagem de linhas rejeitadas.<br/><br/>Os valores permitidos são 1, 2 etc. | Sim, se o **rejectType** for ** porcentagem**. |
| useTypeDefault    | Especifica como tratar valores ausentes em arquivos de texto delimitados quando o PolyBase recuperar dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção Argumentos em [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Os valores permitidos são **True** e **False** (padrão).<br><br> | Não                                            |

### <a name="direct-copy-by-using-polybase"></a>Cópia direta usando PolyBase

O SQL Data Warehouse PolyBase suporta diretamente o Azure Blob, o Azure Data Lake Storage Gen1 e o Azure Data Lake Storage Gen2. Se os dados de origem atenderem aos critérios descritos nesta seção, use o PolyBase para copiar diretamente do armazenamento de dados de origem para o Azure Synapse Analytics. Caso contrário, use [cópia Staged usando PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Para copiar dados de forma eficiente no SQL Data Warehouse, aprender mais com [o Azure Data Factory torna ainda mais fácil e conveniente descobrir insights de dados ao usar data lake store com SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem atendidos, o Azure Data Factory verificará as configurações e retornará automaticamente ao mecanismo BULKINSERT para a movimentação de dados.

1. O **serviço vinculado à origem** está com os seguintes tipos e métodos de autenticação:

    | Tipo de armazenamento de dados de origem suportada                             | Tipo de autenticação de origem suportada                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | Autenticação da chave da conta, autenticação gerenciada de identidade |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autenticação de entidade de serviço                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autenticação da chave da conta, autenticação gerenciada de identidade |

    >[!IMPORTANT]
    >Se o armazenamento do Azure estiver configurado com o ponto final do serviço VNet, você deve usar autenticação de identidade gerenciada - consulte [o impacto de usar os pontos finais do serviço VNet com o armazenamento Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Aprenda as configurações necessárias na Fábrica de Dados do [Azure Blob - autenticação de identidade gerenciada](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 - seção de autenticação de identidade gerenciada,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.

2. O formato de **dados de origem** é de **Parquet,** **ORC**ou **texto delimitado,** com as seguintes configurações:

   1. O caminho da pasta não contém filtro curinga.
   2. O nome do arquivo está vazio ou aponta para um único arquivo. Se você especificar o nome do arquivo `*` curinga na atividade de cópia, ele só pode ser ou `*.*`.
   3. `rowDelimiter`é **padrão,** **\n,** **\r\n,** ou **\r**.
   4. `nullValue`é deixado como padrão ou definido como `treatEmptyAsNull` string **vazio** ("") e é deixado como padrão ou definido como true.
   5. `encodingName`é deixado como padrão ou definido como **utf-8**.
   6. `quoteChar`, `escapeChar`e `skipLineCount` não são especificados. Linha de cabeçalho de salto de `firstRowAsHeader` suporte do PolyBase, que pode ser configurada como no ADF.
   7. `compression`pode não haver **compressão,** **GZip**ou **Deflate**.

3. Se a sua fonte `recursive` for uma pasta, a atividade de cópia deve ser definida como verdadeira.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` e não são especificados.

>[!NOTE]
>Se sua fonte for uma pasta, nota Que o PolyBase recuperará arquivos da pasta e de todas as suas subpastas, e não recuperará dados de arquivos para os quais o nome do arquivo começa com um sublinhado (_) ou um período (.), conforme documentado [aqui - argumento LOCAL](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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

Quando os dados de origem não forem nativamente compatíveis com o PolyBase, habilite a cópia de dados por meio de uma instância de armazenamento azure Blob provisória (não pode ser o Azure Premium Storage). Neste caso, o Azure Data Factory converte automaticamente os dados para atender aos requisitos de formato de dados do PolyBase. Em seguida, ele invoca o PolyBase para carregar dados no SQL Data Warehouse. Finalmente, ele limpa seus dados temporários do armazenamento de blobs. Consulte [Cópia preparada](copy-activity-performance-features.md#staged-copy) para obter detalhes sobre a cópia de dados por meio de uma instância de armazenamento de Blob do Azure de preparação.

Para usar esse recurso, crie um [serviço vinculado ao Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) que se refere à conta de armazenamento Do Zure com o armazenamento intermediário blob. Em seguida, especifique as `enableStaging` propriedades para `stagingSettings` a Atividade de cópia conforme mostrado no código a seguir.

>[!IMPORTANT]
>Se o seu armazenamento azure está configurado com o ponto final do serviço VNet, você deve usar autenticação de identidade gerenciada - consulte [o impacto de usar os pontos finais do serviço VNet com o armazenamento Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Aprenda as configurações necessárias na Fábrica de Dados do [Azure Blob - autenticação de identidade gerenciada](connector-azure-blob-storage.md#managed-identity).

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

As seções a seguir fornecem práticas recomendadas, além das mencionadas nas [melhores práticas para o Azure Synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Permissão de banco de dados obrigatória

Para usar o PolyBase, o usuário que carrega dados no SQL Data Warehouse deve ter a [permissão "CONTROLE"](https://msdn.microsoft.com/library/ms191291.aspx) no banco de dados de destino. Uma maneira de conseguir isso é adicionar o usuário como membro da função **db_owner**. Aprenda como fazer isso na [visão geral do SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Limites de tamanho de linha e tipo de dados

As cargas do PolyBase são limitadas a linhas menores que 1 MB. Não pode ser usado para carregar para VARCHR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX). Para obter mais informações, consulte [Limites de capacidade do serviço SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando os dados de origem tiverem linhas maiores que 1 MB, talvez você queira dividir verticalmente as tabelas de origem em várias pequenas. Certifique-se de que o maior tamanho de cada linha não exceda o limite. As tabelas menores podem então ser carregadas usando o PolyBase e fundidas no Azure Synapse Analytics.

Alternativamente, para dados com colunas tão largas, você pode usar o Non-PolyBase para carregar os dados usando o ADF, desligando a configuração "permitir PolyBase".

#### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse

Para obter a melhor taxa de transferência possível, atribua uma classe de recurso maior para o usuário que carrega dados em SQL Data Warehouse por meio do PolyBase.

#### <a name="polybase-troubleshooting"></a>Solucionando problemas do PolyBase

**Carregamento para coluna Decimal**

Se os dados de origem forem em formato de texto ou em outras lojas não compatíveis com o PolyBase (usando cópia em estágio e PolyBase), e contiver valor vazio a ser carregado na coluna Decimal do SQL Data Warehouse, você pode acertar o seguinte erro:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A solução é desmarcar a opção "**Usar padrão de tipo**" (como falsa) em configurações de dissipação de atividade de cópia - > Configurações do PolyBase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" é uma configuração nativa do PolyBase, que especifica como lidar com valores ausentes em arquivos de texto delimitados quando o PolyBase recupera dados do arquivo de texto. 

**`tableName`no Azure Synapse Analytics**

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

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Use a declaração COPY para carregar dados no Azure SQL Data Warehouse (visualização)

A [instrução](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) SQL Data Warehouse COPY (preview) suporta diretamente os dados de carregamento do **Azure Blob e do Azure Data Lake Storage Gen2**. Se os dados de origem atenderem aos critérios descritos nesta seção, você pode optar por usar a declaração COPY no ADF para carregar dados no Azure SQL Data Warehouse. A Fábrica de Dados do Azure verifica as configurações e falha na execução da atividade de cópia se os critérios não forem atendidos.

>[!NOTE]
>Atualmente, a Data Factory só suporta cópia de fontes compatíveis com a declaração COPY mencionadas abaixo.

O uso da declaração COPY suporta a seguinte configuração:

1. O **serviço e o formato vinculados à origem** estão com os seguintes tipos e métodos de autenticação:

    | Tipo de armazenamento de dados de origem suportada                             | Formato suportado           | Tipo de autenticação de origem suportada                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | [Texto delimitado](format-delimited-text.md)             | Autenticação da chave da conta, autenticação de assinatura de acesso compartilhado, autenticação principal do serviço, autenticação gerenciada de identidade |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autenticação da chave da conta, autenticação de assinatura de acesso compartilhado |
    | &nbsp;                                                       | [Orc](format-orc.md)                        | Autenticação da chave da conta, autenticação de assinatura de acesso compartilhado |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Texto delimitado](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[Orc](format-orc.md) | Autenticação da chave da conta, autenticação principal do serviço, autenticação gerenciada de identidade |

    >[!IMPORTANT]
    >Se o armazenamento do Azure estiver configurado com o ponto final do serviço VNet, você deve usar autenticação de identidade gerenciada - consulte [o impacto de usar os pontos finais do serviço VNet com o armazenamento Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Aprenda as configurações necessárias na Fábrica de Dados do [Azure Blob - autenticação de identidade gerenciada](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 - seção de autenticação de identidade gerenciada,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.

2. As configurações de formato estão com as seguintes:

   1. Para **Parquet**: `compression` não pode haver **compressão,** **Snappy**ou **GZip**.
   2. Para **ORC** `compression` : não pode **```zlib```** haver **compressão,** ou **Snappy**.
   3. Para **texto delimitado:**
      1. `rowDelimiter`é explicitamente definido como **caractere único** ou "**\r\n**", o valor padrão não é suportado.
      2. `nullValue`é deixado como padrão ou definido como **string vazio** ("").
      3. `encodingName`é deixado como padrão ou definido como **utf-8 ou utf-16**.
      4. `escapeChar`deve ser `quoteChar`o mesmo que , e não está vazio.
      5. `skipLineCount`é deixado como padrão ou definido como 0.
      6. `compression`pode não ser **compressão** ou **GZip**.

3. Se a sua fonte `recursive` for uma pasta, a atividade de cópia deve ser definida como verdadeira.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` e não são especificados.

As seguintes configurações de `allowCopyCommand` declaração COPY são suportadas em atividade de cópia:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| Defaultvalues | Especifica os valores padrão de cada coluna de destino no SQL DW.  Os valores padrão na propriedade sobreescrevem a restrição DEFAULT definida no data warehouse e a coluna de identidade não podem ter um valor padrão. | Não |
| Additionaloptions | Opções adicionais que serão passadas para a declaração SQL DW COPY diretamente na cláusula "Com" na [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Cite o valor conforme necessário para se alinhar com os requisitos da declaração COPY. | Não |

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


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Obter propriedades de atividade do Metadata

Para saber detalhes sobre as propriedades, verifique a [atividade getMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo de dados do SQL Data Warehouse do Azure

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar em tabelas do Azure Synapse Analytics. Para obter mais informações, consulte a [transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) no mapeamento dos fluxos de dados.

### <a name="source-transformation"></a>Transformação de origem

As configurações específicas do Azure Synapse Analytics estão disponíveis na guia **Opções** de Origem da transformação de origem. 

**Entrada:** Selecione se você aponta sua fonte ```Select * from <table-name>```para uma tabela (equivalente a ) ou insere uma consulta SQL personalizada.

**Consulta :** Se você selecionar Consulta no campo de entrada, digite uma consulta SQL para sua fonte. Esta configuração substitui qualquer tabela escolhida no conjunto de dados. **Ordem Por** cláusulas não são suportadas aqui, mas você pode definir uma declaração SELECT FROM completa. Você também pode usar funções de tabela definidas pelo usuário. **selecione * a partir de udfGetData()** é um UDF no SQL que retorna uma tabela. Esta consulta produzirá uma tabela de origem que você pode usar no seu fluxo de dados. Usar consultas também é uma ótima maneira de reduzir linhas para testes ou para consultas. 

* Exemplo sql:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Tamanho do lote**: Digite um tamanho de lote para repassar dados grandes em leituras. Nos fluxos de dados, o ADF usará essa configuração para definir o cache colunar Spark. Este é um campo de opção que usará padrões de Faísca se ele for deixado em branco.

**Nível de isolamento**: O padrão para fontes SQL no mapeamento do fluxo de dados é lido sem compromisso. Você pode alterar o nível de isolamento aqui para um desses valores:
* Leitura confirmada
* Ler não comprometido
* Leitura repetida
* Serializável
* Nenhum (ignorar o nível de isolamento)

![Nível de Isolamento](media/data-flow/isolationlevel.png "Nível de Isolamento")

### <a name="sink-transformation"></a>Transformação da pia

As configurações específicas do Azure Synapse Analytics estão disponíveis na guia **Configurações** da transformação do dissipador.

**Método de atualização:** Determina quais operações são permitidas no destino do seu banco de dados. O padrão é permitir apenas inserções. Para atualizar, upsert ou excluir linhas, uma transformação de alter-row é necessária para marcar linhas para essas ações. Para atualizações, upserts e exclusões, uma coluna de chave ou colunas devem ser definidas para determinar qual linha alterar.

**Ação da tabela:** Determina se deve recriar ou remover todas as linhas da tabela de destino antes de escrever.
* Nenhuma: nenhuma ação será feita à mesa.
* Recriar: A tabela será derrubada e recriada. Necessário se criar uma nova tabela dinamicamente.
* Truncate: Todas as linhas da tabela de destino serão removidas.

**Habilitar a encenação:** Determina se deve ou não usar [o PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) ao escrever no Azure Synapse Analytics

**Tamanho do lote**: Controla quantas linhas estão sendo escritas em cada balde. Tamanhos de lote maiores melhoram a otimização de compactação e memória, mas correm o risco de exceções de memória ao cachear dados.

**Scripts Pré e Pós SQL**: Digite scripts SQL de várias linhas que executarão antes (pré-processamento) e depois que (pós-processamento) os dados forem gravados no banco de dados sink

![pré e postar scripts de processamento SQL](media/data-flow/prepost1.png "Scripts de processamento SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapeamento do tipo de dados para Azure Synapse Analytics

Quando você copia dados de ou para o Azure Synapse Analytics, os seguintes mapeamentos são usados dos tipos de dados do Azure Synapse Analytics para os tipos de dados provisórios do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o coletor.

>[!TIP]
>Consulte os tipos de dados da Tabela no artigo [do Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) sobre os tipos de dados suportados pelo SQL DW e as soluçãos para os não suportados.

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
