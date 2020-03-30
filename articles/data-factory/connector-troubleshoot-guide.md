---
title: Solucionar problemas de conectores do Azure Data Factory
description: Saiba como solucionar problemas de conectores na Fábrica de Dados do Azure.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778219"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Solucionar problemas de conectores do Azure Data Factory

Este artigo explora métodos comuns de solução de problemas para conectores na Fábrica de Dados Azure.
  

## <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

### <a name="error-code--azurebloboperationfailed"></a>Código de erro: AzureBlobOperationFailed

- **Mensagem:**`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa**: Problema de impacto da operação de armazenamento blob.

- **Recomendação**: Verifique o erro em detalhes. Consulte o documento de https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codesajuda da bolha: . Entre em contato com a equipe de armazenamento se precisar de ajuda.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Código de erro: AzureBlobServiceNotReturnExpectedDataLength

- **Mensagem:**`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Código de erro: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Mensagem:**`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Código de erro: AzureStorageOperaçãoFailedConcurrentWrite

- **Mensagem:**`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Mensagem de erro: o tamanho da solicitação é muito grande

- **Sintomas**: Você copia dados no Azure Cosmos DB com tamanho padrão do lote de gravação e erro de acerto *" O tamanho da solicitação é muito**grande**".*

- **Causa**: Cosmos DB limita o tamanho de uma única solicitação a 2 MB. A fórmula é: Tamanho da solicitação = Tamanho do documento único * Tamanho do lote de gravação. Se o tamanho do documento for grande, o comportamento padrão resultará em um tamanho de solicitação muito grande. Você pode ajustar o tamanho do lote de gravação.

- **Resolução**: No dissipador de atividade de cópia, reduza o valor 'Gravar tamanho do lote' (o valor padrão é 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Mensagem de erro: Violação exclusiva de restrição de índice

- **Sintomas**: Ao copiar dados no Cosmos DB, você acerte o seguinte erro:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Causa:** Existem duas causas possíveis:

    - Se você usar **Inserir** como comportamento de gravação, esse erro significa que os dados de origem têm linhas/objetos com o mesmo ID.

    - Se você usar **o Upsert** como comportamento de gravação e definir outra chave única para o contêiner, esse erro significa que os dados de origem têm linhas/objetos com IDs diferentes, mas o mesmo valor para a chave única definida.

- **Resolução**: 

    - Para a causa1, configure **Upsert** como comportamento de gravação.
    - Para a causa 2, certifique-se de que cada documento tenha um valor diferente para uma chave única definida.

### <a name="error-message-request-rate-is-large"></a>Mensagem de erro: A taxa de solicitação é grande

- **Sintomas**: Ao copiar dados no Cosmos DB, você acerte o seguinte erro:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Causa**: As unidades de solicitação utilizadas são maiores do que a RU disponível configurada no Cosmos DB. Saiba como cosmos DB calcula RU a partir [daqui](../cosmos-db/request-units.md#request-unit-considerations).

- **Resolução**: Aqui estão duas soluções:

    1. **Aumente o contêiner RU** para maior valor no Cosmos DB, o que melhorará o desempenho da atividade de cópia, embora incorra em mais custo no Cosmos DB. 

    2. Diminuir **writeBatchSize** para um valor menor (como 1000) e definir **paraleloCópias** para um valor menor, como 1, o que tornará o desempenho de execução de cópia pior do que o atual, mas não incorrerá em mais custo no Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Coluna ausente no mapeamento da coluna

- **Sintomas**: Quando você importa esquema para Cosmos DB para mapeamento de colunas, algumas colunas estão faltando. 

- **Causa**: AAD infere o esquema dos primeiros 10 documentos do Cosmos DB. Se algumas colunas/propriedades não tiverem valor nesses documentos, elas não serão detectadas pela ADF, portanto, não aparecerão.

- **Resolução**: Você pode ajustar a consulta como abaixo para forçar a coluna a aparecer em resultado definido com valor vazio: (suponha que a coluna "impossível" está faltando nos primeiros 10 documentos). Alternativamente, você pode adicionar manualmente a coluna para mapeamento.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Mensagem de erro: A representação guia para o leitor é CSharpLegacy

- **Sintomas**: Ao copiar dados do Cosmos DB MongoAPI/MongoDB com campo UUID, você acerte o seguinte erro:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Causa**: Existem duas maneiras de representar uuid em BSON - UuidStardard e UuidLegacy. Por padrão, o UuidLegacy é usado para ler dados. Você acertará o erro se seus dados UUID no MongoDB forem UuidStandard.

- **Resolução**: Na seqüência de conexão MongoDB, adicione a opção "**uuidRepresentation=standard**". Para obter mais informações, consulte [a seqüência de conexões MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Código de erro: AdlsGen2OperaçãoFalha

- **Mensagem:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causa**: ADLS Gen2 lança o erro indicando falha da operação.

- **Recomendação**: Verifique a mensagem de erro detalhada lançada pelo ADLS Gen2. Se for causado por falha passageira, por favor tente novamente. Se precisar de mais ajuda, entre em contato com o suporte do Azure Storage e forneça o ID de solicitação na mensagem de erro.

- **Causa**: Quando a mensagem de erro contém 'Proibido', o principal do serviço ou a identidade gerenciada que você usa podem não ter permissão suficiente para acessar o ADLS Gen2.

- **Recomendação**: Consulte o https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authenticationdocumento de ajuda: .

- **Causa**: Quando a mensagem de erro contém 'InternalServerError', o erro é retornado pelo ADLS Gen2.

- **Recomendação**: Pode ser causada por falha passageira, por favor, tente novamente. Se o problema persistir, entre em contato com o suporte do Azure Storage e forneça o ID de solicitação na mensagem de erro.


### <a name="error-code--adlsgen2invalidurl"></a>Código de erro: AdlsGen2InvalidUrl

- **Mensagem:**`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Código de erro: AdlsGen2InvalidFolderPath

- **Mensagem:**`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Código de erro: AdlsGen2OperaçãoFailedConcurrentWrite

- **Mensagem:**`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Código de erro: AdlsGen2TimeoutError

- **Mensagem:**`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Mensagem de erro: O servidor remoto retornou um erro: (403) Proibido

- **Sintomas**: Falha na atividade de cópia com o seguinte erro: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Causa**: Uma possível causa é que o principal do serviço ou a identidade gerenciada que você usa não tem permissão para acessar determinada pasta/arquivo.

- **Resolução**: Conceda permissões correspondentes em todas as pastas e subpastas que você precisa copiar. Consulte [este doutor.](connector-azure-data-lake-store.md#linked-service-properties)

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Mensagem de erro: Falha ao obter o token de acesso usando o diretor do serviço. Erro ADAL: service_unavailable

- **Sintomas**: Falha na atividade de cópia com o seguinte erro:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Causa**: Quando o Service Token Server (STS) de propriedade do Azure Active Directory não estiver disponível, ou seja, muito ocupado para lidar com solicitações, ele retorna um erro HTTP 503. 

- **Resolução**: Reexecute a atividade de cópia após vários minutos.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Código de erro: SqlFailedToConnect

- **Mensagem:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Causa**: Se a mensagem de erro contiver "SqlException", o Banco de Dados SQL será o erro que indica que alguma operação específica falhou.

- **Recomendação**: Por favor, pesquise por código de https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorserro SQL neste doc de referência para obter mais detalhes: . Se precisar de mais ajuda, entre em contato com o suporte sql do Azure.

- **Causa:** Se a mensagem de erro contiver "Cliente com endereço IP '...". não é permitido acessar o servidor", e você está tentando se conectar ao Banco de Dados SQL do Azure, geralmente é causado pelo problema de firewall do Banco de Dados SQL do Azure.

- **Recomendação**: Na configuração de firewall do Azure SQL Server, habilite a opção "Permitir que os serviços e recursos do Azure acessem esse servidor". Doc de https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configurereferência: .


### <a name="error-code--sqloperationfailed"></a>Código de erro: SqlOperationFailed

- **Mensagem:**`A database operation failed. Please search error to get more details.`

- **Causa**: Se a mensagem de erro contiver "SqlException", o Banco de Dados SQL será o erro que indica que alguma operação específica falhou.

- **Recomendação**: Se o erro SQL não estiver claro, tente alterar o banco de dados para o nível de compatibilidade mais recente '150'. Ele pode lançar erros SQL da versão mais recente. Por favor, indique o doc detalhe: https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat.
        Para solução de problemas de SQL, procure por código de erro https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorsSQL neste doc de referência para obter mais detalhes: . Se precisar de mais ajuda, entre em contato com o suporte sql do Azure.

- **Causa**: Se a mensagem de erro contiver "PdwManagedToNativeInteropException", geralmente é causada por incompatibilidade entre os tamanhos da coluna de origem e do afundamento.

- **Recomendação**: Verifique o tamanho das colunas de origem e pia. Se precisar de mais ajuda, entre em contato com o suporte sql do Azure.

- **Causa**: Se a mensagem de erro contiver "InvalidOperationException", geralmente é causada por dados de entrada inválidos.

- **Recomendação**: Para identificar qual linha encontra o problema, habilite o recurso de tolerância a falhas na atividade de cópia, que pode redirecionar linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Doc de https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerancereferência: .


### <a name="error-code--sqlunauthorizedaccess"></a>Código de erro: SqlUnauthorizedAccess

- **Mensagem:**`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: A credencial está incorreta ou a conta de login não pode acessar o Banco de Dados SQL.

- **Recomendação**: Verifique se a conta de login tem permissão suficiente para acessar o Banco de Dados SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Código de erro: SqlOpenConnectionTimeout

- **Mensagem:**`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa:** Pode ser falha transitória do Banco de Dados SQL.

- **Recomendação**: Por favor, tente novamente atualizar a seqüência de conexão de serviço vinculada com maior valor de tempo de tempo de conexão.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Código de erro: SqlAutoCreateTableTypeMapFailed

- **Mensagem:**`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa**: A tabela de criação automática não pode atender aos requisitos de origem.

- **Recomendação**: Atualize o tipo de coluna em 'mapeamentos', ou crie manualmente a tabela de sumidouro no servidor de destino.


### <a name="error-code--sqldatatypenotsupported"></a>Código de erro: SqlDataTypeNotSupported

- **Mensagem:**`A database operation failed. Check the SQL errors.`

- **Causa**: Se o problema acontecer na fonte SQL e o erro estiver relacionado ao estouro do SqlDateTime, o valor dos dados será superior ao intervalo de tipo lógico (1/1/1753 12:00:00 - 31/12/9999 11:59:59 PM).

- **Recomendação**: Lançar o tipo para string in source SQL consulta ry ou no mapeamento da coluna de atividade de cópia alterar o tipo de coluna para 'String'.

- **Causa**: Se o problema acontecer no dissipador SQL e o erro estiver relacionado ao estouro do SqlDateTime, o valor dos dados será superior ao intervalo permitido na tabela de sumidouros.

- **Recomendação**: Atualize o tipo de coluna correspondente para o tipo 'datetime2' na tabela de sumidouros.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Código de erro: SqlInvalidDbStoredProcedure

- **Mensagem:**`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa**: O procedimento armazenado especificado não é válido. Pode ser causado por que o procedimento armazenado não retornará nenhum dado.

- **Recomendação**: Validar o procedimento armazenado por ferramentas SQL. Certifique-se de que o procedimento armazenado pode retornar dados.


### <a name="error-code--sqlinvaliddbquerystring"></a>Código de erro: SqlInvalidDbQueryString

- **Mensagem:**`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa**: A consulta SQL especificada não é válida. Pode ser causado por que a consulta não retorna nenhum dado

- **Recomendação**: Validar a consulta SQL por ferramentas SQL. Certifique-se de que a consulta pode retornar dados.


### <a name="error-code--sqlinvalidcolumnname"></a>Código de erro: SqlInvalidColumnName

- **Mensagem:**`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa:** Não é possível encontrar coluna. Possível configuração errada.

- **Recomendação**: Verifique a coluna na consulta, 'estrutura' no conjunto de dados e 'mapeamentos' em atividade.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Código de erro: SqlColumnNameMismatchByCaseSensitive

- **Mensagem:**`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Código de erro: SqlBatchWriteTimeout

- **Mensagem:**`Timeouts in SQL write operation.`

- **Causa:** Pode ser falha transitória do Banco de Dados SQL.

- **Recomendação**: Por favor, tente novamente. Se houver problema sumido, entre em contato com o suporte sql do Azure.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Código de erro: SqlBatchWriteTransactionFailed

- **Mensagem:**`SQL transaction commits failed`

- **Causa**: Se os detalhes da exceção informarem constantemente o tempo limite da transação, a latência da rede entre o tempo de execução de integração e o banco de dados é maior do que o limite padrão de 30 segundos.

- **Recomendação**: Atualizar string de conexão de serviço vinculado sql com valor 'tempo limite de conexão' é igual a 120 ou mais e executar a atividade.

- **Causa**: Se os detalhes da exceção contarem intermitentemente que a conexão sqlestá quebrada, pode ser apenas falha de rede transitória ou problema lateral do banco de dados SQL

- **Recomendação**: Por favor, tente novamente a atividade e revise as métricas laterais do Banco de Dados SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Código de erro: SqlBulkCopyInvalidColumnLength

- **Mensagem:**`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: SQL Bulk Copy falhou devido ao recebimento de um comprimento de coluna inválido do cliente bcp.

- **Recomendação**: Para identificar qual linha encontra o problema, habilite o recurso de tolerância a falhas na atividade de cópia, que pode redirecionar linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Doc de https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerancereferência: .


### <a name="error-code--sqlconnectionisclosed"></a>Código de erro: SqlConnectionÉFechado

- **Mensagem:**`The connection is closed by SQL Database.`

- **Causa**: A conexão SQL é fechada pelo Banco de Dados SQL quando a alta execução simultânea e a conexão de servidor.

- **Recomendação**: O servidor remoto fechou a conexão SQL. Tente novamente. Se houver problema sumido, entre em contato com o suporte sql do Azure.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Código de erro: SqlCreateTableFailedUnsupportedType

- **Mensagem:**`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Mensagem de erro: Falha de conversão ao converter de uma seqüência de caracteres para identificador exclusivo

- **Sintomas:** Quando você copia dados da fonte de dados tabular (como o SQL Server) no Azure SQL Data Warehouse usando cópia em estágio e PolyBase, você acerte o seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Causa**: O Azure SQL Data Warehouse PolyBase não pode converter string vazia em GUID.

- **Resolução**: No dissipador de atividade sumido, nas configurações do Polybase, defina a opção "**use type default**" como falsa.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Mensagem de erro: Tipo de dados esperado: DECIMAL(x,x), Valor ofensivo

- **Sintomas:** Quando você copia dados da fonte de dados tabular (como sql server) em SQL DW usando cópia em estágio e PolyBase, você acerte o seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Causa**: A Azure SQL Data Warehouse Polybase não pode inserir string vazia (valor nulo) na coluna decimal.

- **Resolução**: No dissipador de atividade sumido, nas configurações do Polybase, defina a opção "**use type default**" como falsa.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Mensagem de erro: mensagem de exceção Java:HdfsBridge:CreateRecordReader

- **Sintomas**: Você copia dados no Azure SQL Data Warehouse usando o PolyBase e acerta o seguinte erro:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Causa**: A possível causa é que o esquema (largura total da coluna) é muito grande (maior que 1 MB). Verifique o esquema da tabela SQL DW de destino adicionando o tamanho de todas as colunas:

    - Int -> 4 bytes
    - Bigint -> 8 bytes
    - Varchar(n),char(n), binário(n), varbinary(n) -> n bytes
    - Nvarchar(n), nchar(n) -> n*2 bytes
    - Data -> 6 bytes
    - Data/(2), data-hora pequena -> 16 bytes
    - Datetimeoffset -> 20 bytes
    - Decimal -> 19 bytes
    - Flutuar -> 8 bytes
    - Dinheiro -> 8 bytes
    - Smallmoney -> 4 bytes
    - Real -> 4 bytes
    - Smallint -> 2 bytes
    - Tempo -> 12 bytes
    - Tinyint -> 1 byte

- **Resolução**: Reduza a largura da coluna para menos de 1 MB

- Ou usar a abordagem de inserção em massa desabilitando o Polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Mensagem de erro: A condição especificada usando cabeçalho condicional HTTP não é atendida

- **Sintomas**: Você usa consulta SQL para extrair dados do Azure SQL Data Warehouse e acertar o seguinte erro:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa**: O Azure SQL Data Warehouse acertou o problema de consulta à tabela externa no Azure Storage.

- **Resolução**: Execute a mesma consulta no SSMS e verifique se você vê o mesmo resultado. Em caso afirmativo, abra um tíquete de suporte para o SQL Data Warehouse do Azure e forneça o servidor e o nome do banco de dados do SQL Data Warehouse para prosseguir com a solução de problemas.
            

## <a name="delimited-text-format"></a>Formato de texto delimitado

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Código de erro: DelimitedTextColumnNameNotAllowNull

- **Mensagem:**`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa**: Quando definido 'firstRowAsHeader' em atividade, a primeira linha será usada como nome da coluna. Este erro significa que a primeira linha contém valor vazio. Por exemplo: 'ColumnA,,ColumnB'.

- **Recomendação**: Verifique a primeira linha e corrija o valor se houver valor vazio.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Código de erro: DelimitedTextMoreColumnsThanDefined

- **Mensagem:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Causa**: A contagem de colunas da linha problemática é maior do que a contagem da coluna da primeira linha. Pode ser causada por problema de dados ou configurações incorretas de delimitador/quote char.

- **Recomendação**: Por favor, obtenha a contagem de linhas na mensagem de erro, verifique a coluna da linha e corrija os dados.

- **Causa**: Se a contagem de colunas esperada for "1" na mensagem de erro, é possível que você tenha especificado configurações erradas de compactação ou formato, o que fez com que o ADF analisasse erroneamente seu arquivo(s).

- **Recomendação**: Verifique as configurações de formato para certificar-se de que corresponde aos arquivos de origem.

- **Causa**: Se sua fonte é uma pasta, é possível que os arquivos a pasta especificada tenham esquemas diferentes.

- **Recomendação**: Certifique-se de que os arquivos a pasta dada têm esquema idêntico.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Código de erro: DelimitedTextIncorrectRowDelimiter

- **Mensagem:**`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Código de erro: DelimitedTextTooLargeColumnCount

- **Mensagem:**`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Código de erro: DelimitedTextInvalidSettings

- **Mensagem:**`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dinâmica 365/Serviço de dados comum/CRM dinâmico

### <a name="error-code--dynamicscreateserviceclienterror"></a>Código de erro: DynamicsCreateServiceClientError

- **Mensagem:**`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Causa**: Este é um problema transitório no lado do servidor dinâmico.

- **Recomendação**: Reexecutar o gasoduto. Se continuar falhando, tente reduzir o paralelismo. Se ainda falhar, entre em contato com o suporte da dinâmica.



## <a name="json-format"></a>Format JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Código de erro: JsonInvalidArrayPathDefinition

- **Mensagem:**`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Código de erro: JsonEmptyJObjectData

- **Mensagem:**`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Código de erro: JsonNullValueInPathDefinition

- **Mensagem:**`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Código de erro: JsonUnsupportedHierarchicalComplexValue

- **Mensagem:**`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Código de erro: JsonConflictPartitionDiscoverySchema

- **Mensagem:**`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Código de erro: JsonInvalidDataFormat

- **Mensagem:**`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Código de erro: JsonInvalidDataMixedArrayAndObject

- **Mensagem:**`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Formato de Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Código de erro: ParquetJavaInvocationException

- **Mensagem:**`An error occurred when invoking java, message: %javaException;.`

- **Causa**: Quando a mensagem de erro contém 'java.lang.OutOfMemory', 'Java heap space' e 'doubleCapacity', geralmente é um problema de gerenciamento de memória na versão antiga do tempo de execução de integração.

- **Recomendação**: Se você estiver usando o Executtime de Integração Auto-hospedado e a versão for anterior a 3.20.7159.1, sugiro atualizar para a versão mais recente.

- **Causa**: Quando a mensagem de erro contém 'java.lang.OutOfMemory', o tempo de execução de integração não tem recursos suficientes para processar o arquivo(s).

- **Recomendação**: Limitar as corridas simultâneas no tempo de execução da integração. Para o Runtime de integração auto-hospedado, dimensione até uma máquina poderosa com memória igual ou superior a 8 GB.

- **Causa**: Quando a mensagem de erro contém 'NullPointerReference', é possível que seja um erro transitório.

- **Recomendação**: Por favor, tente novamente. Se o problema persistir, entre em contato com o suporte.


### <a name="error-code--parquetinvalidfile"></a>Código de erro: ParquetInvalidFile

- **Mensagem:**`File is not a valid parquet file.`

- **Causa:** Problema do arquivo Parquet.

- **Recomendação**: Verifique se a entrada é um arquivo parquet válido.


### <a name="error-code--parquetnotsupportedtype"></a>Código de erro: ParquetNotSupportedType

- **Mensagem:**`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa**: O formato parquet não é suportado na Fábrica de Dados Azure.

- **Recomendação**: Verifique duas vezes os dados de origem. Consulte o doutor: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Código de erro: ParquetMissedDecimalPrecisionScale

- **Mensagem:**`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa**: Tente analisar o número de precisão e escala, mas nenhuma dessas informações é fornecida.

- **Recomendação**: 'Fonte' não retorna precisão e escala corretas. Verifique a precisão e a escala da coluna de emissão.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Código de erro: ParquetInvalidDecimalPrecisionScale

- **Mensagem:**`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa:** O esquema é inválido.

- **Recomendação**: Verifique a precisão e a escala da coluna de emissão.


### <a name="error-code--parquetcolumnnotfound"></a>Código de erro: ParquetColumnNotFound

- **Mensagem:**`Column %column; does not exist in Parquet file.`

- **Causa**: O esquema de origem é incompatível com o esquema da pia.

- **Recomendação**: Verifique os 'mapeamentos' em 'atividade'. Certifique-se de que a coluna de origem pode ser mapeada para a coluna da pia direita.


### <a name="error-code--parquetinvaliddataformat"></a>Código de erro: ParquetInvalidDataFormat

- **Mensagem:**`Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: Os dados não podem ser convertidos em tipo especificado em mappings.source

- **Recomendação**: Verifique duas vezes os dados de origem ou especifique o tipo de dados correto para esta coluna no mapeamento da coluna de atividade de cópia. Consulte o doutor: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Código de erro: ParquetDataCountNotMatchColumnCount

- **Mensagem:**`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa**: Contagem da coluna de origem e incompatibilidade de contagem de colunas de pia

- **Recomendação**: A contagem da coluna de origem de verificação dupla é a mesma da contagem da coluna de sumidouro em 'mapeamento'.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Código de erro: ParquetDataTypeNotMatchColumnType

- **Mensagem**: O tipo de dados %srcType; não é compatível com o tipo de coluna %dstType; na coluna '%columnIndex;'.

- **Causa**: Os dados da fonte não podem ser convertidos em digitados definidos na pia

- **Recomendação**: Por favor, especifique um tipo correto em mapping.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Código de erro: ParquetBridgeInvalidData

- **Mensagem:**`%message;`

- **Causa**: Valor dos dados sobre limitação

- **Recomendação**: Por favor, tente novamente. Se o problema persistir, entre em contato conosco.


### <a name="error-code--parquetunsupportedinterpretation"></a>Código de erro: ParquetUnsupportedInterpretation

- **Mensagem:**`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Causa**: Cenário não suportado

- **Recomendação**: 'ParquetInterpretFor' não deve ser 'sparkSql'.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Código de erro: ParquetUnsupportFileLevelCompressionOption

- **Mensagem:**`File level compression is not supported for Parquet.`

- **Causa**: Cenário não suportado

- **Recomendação**: Remova 'CompressionType' na carga útil.



## <a name="general-copy-activity-error"></a>Erro de atividade da cópia geral

### <a name="error-code--jrenotfound"></a>Código de erro: JreNotFound

- **Mensagem:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa**: O tempo de execução de integração auto-hospedado não pode encontrar Java Runtime. O Java Runtime é necessário para ler uma fonte específica.

- **Recomendação**: Verifique seu ambiente de tempo de execução de integração, o doc de referência:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Código de erro: CuringaPathSinkNão suportado

- **Mensagem:**`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa**: O conjunto de dados sink não suporta curinga.

- **Recomendação**: Verifique o conjunto de dados do dissipador e corrija o caminho sem o valor do curinga.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Código de erro: MapeamentoInvalidPropertyWithEmptyValue

- **Mensagem:**`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Código de erro: MapeamentoInvalidPropertyWithNamePathAndOrdinal

- **Mensagem:**`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Código de erro: MapeamentoDuplicadoDuplical

- **Mensagem:**`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Código de erro: MapeamentoInvalidOrdinalForSinkColumn

- **Mensagem:**`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda para solucionar problemas, tente esses recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recursos da Fábrica de Dados](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Fórum Stack Overflow para Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
            
