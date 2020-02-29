---
title: Tutorial de carregamento de dados de Azure Data Lake Storage
description: Use tabelas externas do polybase para carregar dados de Azure Data Lake Storage para análise de SQL.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9a567a8f62f8f12de725f6d9420576680a3005fe
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194573"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>Carregar dados de Azure Data Lake Storage para análise de SQL
Este guia descreve como usar tabelas externas do polybase para carregar dados de Azure Data Lake Storage. Embora seja possível executar consultas ad hoc em dados armazenados no Data Lake Storage, é recomendável importar os dados para obter o melhor desempenho. 

> [!NOTE]  
> Uma alternativa ao carregamento é a [instrução de cópia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) atualmente em visualização pública.  A instrução COPY fornece mais flexibilidade. Para fornecer comentários sobre a instrução de cópia, envie um email para a seguinte lista de distribuição: sqldwcopypreview@service.microsoft.com.
>
> [!div class="checklist"]

> * Crie objetos de banco de dados necessários para carregar de Data Lake Storage.
> * Conecte-se a um diretório Data Lake Storage.
> * Carregar dados no data warehouse.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar este tutorial, baixe e instale a versão mais recente do [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio).

Para este tutorial, você precisa do:

* Um pool do SQL. Consulte [criar um pool de SQL e consultar dados](create-data-warehouse-portal.md).
* Uma conta de Data Lake Storage. Consulte [introdução ao Azure data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md). Para essa conta de armazenamento, você precisará configurar ou especificar uma das seguintes credenciais a serem carregadas: uma chave de conta de armazenamento, um usuário de aplicativo do Azure Directory ou um usuário do AAD que tem a função RBAC apropriada para a conta de armazenamento. 

##  <a name="create-a-credential"></a>Criar uma credencial
Você pode ignorar esta seção e continuar a "criar a fonte de dados externa" ao autenticar usando passagem do AAD. Uma credencial no escopo do banco de dados não precisa ser criada ou especificada ao usar passagem do AAD, mas verifique se o usuário do AAD tem a função RBAC apropriada (leitor de dados de blob de armazenamento, colaborador ou função de proprietário) para a conta de armazenamento. Mais detalhes são descritos [aqui](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260). 

Para acessar sua conta de Data Lake Storage, você precisará criar uma chave mestra de banco de dados para criptografar seu segredo de credencial. Em seguida, você cria uma credencial no escopo do banco de dados para armazenar seu segredo. Ao autenticar usando entidades de serviço (usuário do aplicativo do Azure Directory), a credencial no escopo do banco de dados armazena as credenciais da entidade de serviço configuradas no AAD. Você também pode usar a credencial no escopo do banco de dados para armazenar a chave da conta de armazenamento para Gen2.

Para se conectar ao Data Lake Storage usando entidades de serviço, **primeiro** você deve criar um aplicativo Azure Active Directory, criar uma chave de acesso e conceder ao aplicativo acesso à conta de data Lake Storage. Para obter instruções, consulte [autenticar para Azure data Lake Storage usando Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Criar a fonte de dados externa
Use este comando [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) para armazenar o local dos dados. Se você estiver autenticando com passagem do AAD, o parâmetro CREDENTIAL não será necessário. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Configurar o formato de dados
Para importar os dados de Data Lake Storage, você precisa especificar o formato de arquivo externo. Esse objeto define como os arquivos são gravados em Data Lake Storage.
Para ver uma lista completa, consulte nossa documentação de T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Criar as tabelas externas
Agora que você especificou o formato do arquivo e da fonte de dados, você está pronto para criar as tabelas externas. As tabelas externas servem para interagir com dados externos. O parâmetro de local pode especificar um arquivo ou diretório. Se ele especificar um diretório, todos os arquivos dentro do diretório serão carregados.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Considerações sobre a tabela externa
É fácil criar uma tabela externa, mas existem algumas nuances que precisam ser abordadas.

As Tabelas Externas são fortemente tipadas. Isso significa que cada linha dos dados que estão sendo ingeridos deve atender à definição de esquema da tabela.
Se uma linha não coincidir com a definição de esquema, a linha é rejeitada da carga.

As opções REJECT_TYPE e REJECT_VALUE permitem que você defina quantas linhas ou qual o percentual de dados deve estar presente na tabela final. Durante o carregamento, se o valor rejeitado for atingido, o carregamento falha. A causa mais comum de rejeição de linhas é uma incompatibilidade da definição do esquema. Por exemplo, se uma coluna é especificada incorretamente para o esquema como inteiros, quando os dados no arquivo são de uma cadeia de caracteres, todas as linhas falham ao carregar.

Data Lake armazenamento Gen1 usa controle de acesso com base da função (RBAC) para controlar o acesso aos dados. Isso significa que a entidade de serviço deve ter permissões de leitura para os diretórios definidos no parâmetro de local e para os filhos do diretório final e dos arquivos. Isso permite ao PolyBase autenticar e carregar os dados. 

## <a name="load-the-data"></a>Carregar os dados
Para carregar dados de Data Lake Storage use a instrução [CREATE TABLE as Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) . 

O CTAS cria uma nova tabela e a preenche com os resultados de uma instrução select. CTAS define a nova tabela para ter as mesmas colunas e tipos de dados como os resultados da instrução select. Se você selecionar todas as colunas de uma tabela externa, a nova tabela será uma réplica das colunas e dos tipos de dados na tabela externa.

Neste exemplo, estamos criando uma tabela distribuída de hash chamada DimProduct da nossa tabela externa DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Otimizar a compactação columnstore
Por padrão, as tabelas são definidas como um índice columnstore clusterizado. Após a conclusão do carregamento, algumas das linhas de dados não podem ser compactadas no columnstore.  Há várias razões para isso acontecer. Para obter mais informações, confira [gerenciar índices columnstore](sql-data-warehouse-tables-index.md).

Para otimizar o desempenho da consulta e a compactação columnstore após um carregamento, recrie a tabela para forçar o índice columnstore a compactar todas as linhas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Otimizar estatísticas
É melhor criar estatísticas de coluna única imediatamente após um carregamento. Há algumas opções de estatísticas. Por exemplo, se você criar estatísticas de coluna única em todas as colunas, poderá levar muito tempo para recompilar todas as estatísticas. Se você souber que determinadas colunas não estarão em predicados de consulta, você poderá ignorar a criação de estatísticas nessas colunas.

Se você decidir criar estatísticas com uma coluna em cada coluna de cada tabela, poderá usar o exemplo de código do procedimento armazenado `prc_sqldw_create_stats` no artigo [estatísticas](sql-data-warehouse-tables-statistics.md).

O exemplo a seguir é um bom ponto de partida para a criação de estatísticas. Ele cria estatísticas de coluna única em cada coluna na tabela de dimensões e em cada coluna de junção das tabelas de fatos. Você poderá adicionar estatísticas de coluna única ou múltipla às colunas de tabelas de fatos posteriormente.

## <a name="achievement-unlocked"></a>Missão cumprida!
Você carregou com êxito os dados no seu data warehouse. Bom trabalho!

## <a name="next-steps"></a>Próximas etapas 
Neste tutorial, você criou tabelas externas para definir a estrutura dos dados armazenados no Data Lake Storage Gen1 e, em seguida, usou a instrução PolyBase CREATE TABLE AS SELECT para carregar dados em seu data warehouse. 

Você fez essas coisas:
> [!div class="checklist"]
>
> * Foram criados objetos de banco de dados necessários para carregar de Data Lake Storage.
> * Conectado a um Data Lake Storage Directory.
> * Dados carregados no data warehouse.
>

Carregar dados é a primeira etapa para desenvolver uma solução de data warehouse usando o Azure Synapse Analytics. Confira nossos recursos de desenvolvimento.

> [!div class="nextstepaction"]
> [Saiba como desenvolver tabelas para data warehousing](sql-data-warehouse-tables-overview.md)
