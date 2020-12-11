---
title: Solucionar problemas de conectores do Azure Data Factory
description: Saiba como solucionar problemas com conector no Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: a7a81a742922d45be965c7f73e3cb910d0ef989a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109276"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Solucionar problemas de conectores do Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para conectores no Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

### <a name="error-code--azurebloboperationfailed"></a>Código de erro:  AzureBlobOperationFailed

- **Mensagem**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa**: A operação do armazenamento de blobs encontrou um problema.

- **Recomendação**:  Verifique os detalhes do erro. Consulte o documento de ajuda de blob: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Contate a equipe de armazenamento se precisar de ajuda.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Código de erro:  AzureBlobServiceNotReturnExpectedDataLength

- **Mensagem**: `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Código de erro:  AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Mensagem**: `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Código de erro:  AzureStorageOperationFailedConcurrentWrite

- **Mensagem**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="invalid-property-during-copy-activity"></a>Propriedade inválida durante a atividade de cópia

- **Mensagem**:  `Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **Causa**: o tipo definido no conjunto de texto é inconsistente com o tipo de origem/coletor definido na atividade de cópia.

- **Resolução**: Edite a definição de JSON de conjunto de forma ou de pipeline para tornar os tipos consistentes e execute a implantação novamente.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Mensagem de erro: O tamanho da solicitação é muito grande

- **Sintomas**: Você copia os dados no Azure Cosmos DB com o tamanho do lote de gravação padrão e encontra o erro *"**O tamanho da solicitação é muito grande**"* .

- **Causa**: O Cosmos DB limita o tamanho da uma solicitação única para 2MB. A fórmula é Tamanho da solicitação = Tamanho do documento único * Tamanho do lote de gravação. Se o documento for grande, o comportamento padrão resultará em uma solicitação grande demais. Você pode ajustar o tamanho do lote de gravação.

- **Resolução:** No coletor de atividade de cópia, reduza o valor de 'Tamanho do lote de gravação' (o valor padrão é 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Mensagem de erro: Violação de restrição de índice exclusivo

- **Sintomas**: Ao copiar dados para o Cosmos DB, ocorreu o seguinte erro:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Causa**: Há duas causas possíveis:

    - Se você usar **Inserir** como comportamento de gravação, esse erro significa que os dados de origem têm linhas/objetos com a mesma ID.

    - Se você usar **Upsert** como comportamento de gravação e definir outra chave exclusiva para o contêiner, esse erro significa que os dados de origem têm linhas/objetos com IDs diferentes, mas o mesmo valor para a chave exclusiva definida.

- **Resolução:** 

    - Para a causa 1, defina **Upsert** como comportamento de gravação.
    - Para a causa 2, verifique se cada documento tem um valor diferente para a chave exclusiva definida.

### <a name="error-message-request-rate-is-large"></a>Mensagem de erro: A taxa de solicitação é alta

- **Sintomas**: Ao copiar dados para o Cosmos DB, ocorreu o seguinte erro:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Causa**: As unidades de solicitação usadas são maiores do que a RU disponível configurada em Cosmos DB. Saiba como Cosmos DB calcula a RU [aqui](../cosmos-db/request-units.md#request-unit-considerations).

- **Resolução:** Existem duas soluções:

    1. **Aumentar o contêiner de RU** para o maior valor no Cosmos DB para melhorar desempenho da atividade de cópia, embora resulte em mais custos no Cosmos DB. 

    2. Diminuir o **writeBatchSize** para um valor menor (como 1000) e definir **parallelCopies** como um valor menor, como 1. Isso tornará o desempenho de execução de cópia pior do que o atual, mas não resultará em custo maior no Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Coluna ausente no mapeamento de coluna

- **Sintomas**: Quando você importa o esquema para Cosmos DB para mapeamento de coluna, algumas colunas estão ausentes. 

- **Causa**: O ADF infere o esquema dos primeiros 10 documentos do Cosmos DB. Se algumas colunas/propriedades não tiverem valor nesses documentos, elas não serão detectadas pelo ADF, portanto, não aparecerão.

- **Resolução:** Você pode ajustar a consulta da maneira mostrada abaixo para impor a coluna a ser exibida no conjunto de resultados com um valor vazio: (suponha que a coluna "impossível" esteja ausente nos 10 primeiros documentos). Como alternativa, você pode adicionar manualmente a coluna para mapeamento.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Mensagem de erro: O GuidRepresentation para o leitor é CSharpLegacy

- **Sintomas**: Ao copiar dados do Cosmos DB MongoAPI/MongoDB com o campo UUID, você encontra o seguinte erro:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Causa**: Há duas maneiras de representar o UUID em BSON-UuidStandard e UuidLegacy. Por padrão, UuidLegacy é usado para ler dados. Você encontrará um erro se os dados do UUID no MongoDB forem UuidStandard.

- **Resolução:** Na cadeia de conexão do MongoDB, adicione a opção “**uuidRepresentation=standard**”. Para obter mais informações, consulte [cadeia de conexão do MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Código de erro:  AdlsGen2OperationFailed

- **Mensagem**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causa**: O ADLS Gen2 gera o erro indicando que a operação falhou.

- **Recomendação**:  Verifique a mensagem de erro detalhada lançada pelo ADLS Gen2. Se ele for causado por uma falha transitória, tente novamente. Se precisar de ajuda adicional, entre em contato com o suporte do Armazenamento do Microsoft Azure e forneça a ID da solicitação na mensagem de erro.

- **Causa**: Quando a mensagem de erro contém “Proibido”, a entidade de serviço ou a identidade gerenciada usada pode não ter permissão suficiente para acessar o ADLS Gen2.

- **Recomendação**:  Consulte o documento de ajuda: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Causa**: Quando a mensagem de erro contém “InternalServerError”, o erro é retornado pelo ADLS Gen2.

- **Recomendação**:  Pode ser causado por uma falha transitória, tente novamente. Se o problema persistir, entre em contato com o suporte do Armazenamento do Microsoft Azure e forneça a ID da solicitação na mensagem de erro.


### <a name="error-code--adlsgen2invalidurl"></a>Código de erro:  AdlsGen2InvalidUrl

- **Mensagem**: `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Código de erro:  AdlsGen2InvalidFolderPath

- **Mensagem**: `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Código de erro:  AdlsGen2OperationFailedConcurrentWrite

- **Mensagem**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>Código de erro: AdlsGen2TimeoutError

- **Mensagem**: `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


### <a name="request-to-adls-gen2-account-met-timeout-error"></a>Solicitação para ADLS Gen2 erro de tempo limite atingido na conta

- **Mensagem**: código de erro = `UserErrorFailedBlobFSOperation` , mensagem de erro = `BlobFS operation failed for: A task was canceled` .

- **Causa**: o problema é causado pelo erro de tempo limite do coletor de ADLS Gen2, que ocorre principalmente na máquina ir hospedada internamente.

- **Recomendação**: 

    1. Coloque sua máquina IR auto-hospedada e direcione a conta ADLS Gen2 na mesma região, se possível. Isso pode evitar um erro de tempo limite aleatório e ter um melhor desempenho.

    1. Verifique se há alguma configuração de rede especial, como o ExpressRoute, e certifique-se de que a rede tenha largura de banda suficiente. É recomendável reduzir a configuração de trabalhos simultâneos de IR hospedados automaticamente quando a largura de banda geral é baixa, por meio do qual pode evitar a competição de recursos de rede em vários trabalhos simultâneos.

    1. Use tamanho de bloco menor para cópia não binária para atenuar esse erro de tempo limite se o tamanho do arquivo for moderado ou pequeno. Consulte o [bloco put do armazenamento de BLOBs](https://docs.microsoft.com/rest/api/storageservices/put-block).

       Para especificar o tamanho do bloco personalizado, você pode editar a propriedade no editor. JSON:
    ```
    "sink": {
        "type": "DelimitedTextSink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings",
            "blockSizeInMB": 8
        }
    }
    ```


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Mensagem de erro: a conexão subjacente foi fechada: não foi possível estabelecer uma relação de confiança para o canal seguro de SSL/TLS.

- **Sintomas**: a atividade de cópia falha com o seguinte erro: 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **Causa**: falha na validação do certificado durante o HANDSHAKE de TLS.

- **Resolução**: solução alternativa: Use cópia em etapas para ignorar a validação de TLS para ADLS Gen1. Você precisa reproduzir esse problema e reunir o rastreamento do Netmon e, em seguida, envolver sua equipe de rede para verificar a configuração de rede local.

    ![Solucionar problemas ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Mensagem de erro: O servidor remoto retornou um erro: (403) Forbidden

- **Sintomas**: Falha na atividade de cópia com o seguinte erro: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Causa**: Uma causa possível é que a entidade de serviço ou a identidade gerenciada que você usa não tem permissão para acessar a pasta e o arquivo específicos.

- **Resolução:** Conceda permissões correspondentes em todas as pastas e subpastas que você precisa copiar. Consulte [este documento](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Mensagem de erro: Falha ao obter o token de acesso usando a entidade de serviço. Erro de ADAL: service_unavailable

- **Sintomas**: Falha na atividade de cópia com o seguinte erro:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Causa**: Quando o Servidor de Token de Serviço (STS) pertencente ao Azure Active Directory não está indisponível, ou seja, muito ocupado para lidar com solicitações, ele retorna um erro HTTP 503. 

- **Resolução:** Execute a atividade de cópia novamente após alguns minutos.
                  

## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure Synapse Analytics/banco de dados SQL do Azure/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Código de erro:  SqlFailedToConnect

- **Mensagem**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Causa**: Se a mensagem de erro contiver “SqlException”, o Banco de Dados SQL lançará o erro indicando que uma operação específica falhou.

- **Recomendação**: Pesquise por código de erro do SQL neste documento de referência para obter mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Se precisar de mais ajuda, entre em contato com o suporte do SQL do Azure.

- **Causa**: Se a mensagem de erro contiver "Cliente com endereço IP '...' não tem permissão para acessar o servidor", e você estiver tentando se conectar ao Banco de Dados SQL do Azure, geralmente o erro é causado por problema do firewall do Banco de Dados SQL do Azure.

- **Recomendação**: em configuração lógica do firewall do SQL Server, habilite a opção "permitir que os serviços e recursos do Azure acessem este servidor". Documento de referência: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Código de erro:  SqlOperationFailed

- **Mensagem**: `A database operation failed. Please search error to get more details.`

- **Causa**: Se a mensagem de erro contiver “SqlException”, o Banco de Dados SQL lançará o erro indicando que uma operação específica falhou.

- **Recomendação**:  Se o erro de SQL não for claro, tente alterar o banco de dados para o nível de compatibilidade mais recente “150”. Isso pode gerar erros de SQL da versão mais recente. Consulte o [documento de detalhes](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Para solucionar problemas de SQL, pesquise pelo código de erro do SQL neste documento de referência para obter mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Se precisar de mais ajuda, entre em contato com o suporte do SQL do Azure.

- **Causa**: Se a mensagem de erro contiver "PdwManagedToNativeInteropException", geralmente ela é causada por incompatibilidade entre os tamanhos de coluna de origem e de coletor.

- **Recomendação**:  Verifique o tamanho das colunas de origem e do coletor. Se precisar de mais ajuda, entre em contato com o suporte do SQL do Azure.

- **Causa**: Se a mensagem de erro contiver "InvalidOperationException", geralmente ela é causada por dados de entrada inválidos.

- **Recomendação**:  Para identificar em qual linha está o problema, habilite o recurso de tolerância a falhas na atividade de cópia, que pode redirecionar as linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Documento de referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.



### <a name="error-code--sqlunauthorizedaccess"></a>Código de erro:  SqlUnauthorizedAccess

- **Mensagem**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: A credencial está incorreta ou a conta de logon não consegue acessar o Banco de Dados SQL.

- **Recomendação**:  Verifique se a conta de logon tem permissão suficiente para acessar o Banco de Dados SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Código de erro:  SqlOpenConnectionTimeout

- **Mensagem**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa**: Pode ser uma falha transitória do Banco de Dados SQL.

- **Recomendação**: tente novamente atualizar a cadeia de conexão de serviço vinculado com um valor de tempo limite de conexão maior.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Código de erro:  SqlAutoCreateTableTypeMapFailed

- **Mensagem**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa**: A tabela de criação automática não pode atender ao requisito de origem.

- **Recomendação**:  Atualize o tipo de coluna em “mapeamentos” ou crie manualmente a tabela de coletor no servidor de destino.


### <a name="error-code--sqldatatypenotsupported"></a>Código de erro:  SqlDataTypeNotSupported

- **Mensagem**: `A database operation failed. Check the SQL errors.`

- **Causa**: Se o problema ocorrer na fonte SQL e o erro estiver relacionado ao estouro de SqlDateTime, o valor dos dados será sobre o intervalo do tipo lógico (1/1/1753 12:00:00 - 31/12/9999 23:59:59).

- **Recomendação**:  Converta o tipo para cadeia de caracteres na consulta SQL de origem ou, no mapeamento de coluna da atividade de cópia, altere o tipo de coluna para “String”.

- **Causa**: Se o problema ocorrer no coletor SQL e o erro estiver relacionado ao estouro de SqlDateTime, o valor dos dados estará acima do intervalo permitido na tabela de coletor.

- **Recomendação**:  Atualize o tipo de coluna correspondente para o tipo “datetime2” na tabela de coletor.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Código de erro:  SqlInvalidDbStoredProcedure

- **Mensagem**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa**: O procedimento armazenado especificado não é válido. Isso pode ser causado por que o procedimento armazenado não retorna nenhum dado.

- **Recomendação**:  Valide o procedimento armazenado pelas Ferramentas do SQL. Verifique se o procedimento armazenado pode retornar dados.


### <a name="error-code--sqlinvaliddbquerystring"></a>Código de erro:  SqlInvalidDbQueryString

- **Mensagem**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa**: A consulta SQL especificada não é válida. Pode ser causado por que a consulta não retorna nenhum dado

- **Recomendação**:  Valide a consulta SQL por ferramentas SQL. Verifique se a consulta pode retornar dados.


### <a name="error-code--sqlinvalidcolumnname"></a>Código de erro:  SqlInvalidColumnName

- **Mensagem**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa**: Não é possível encontrar a coluna. Configuração possível incorreta.

- **Recomendação**:  Verifique a coluna na consulta, “estrutura” no conjunto de dados e “mapeamentos” na atividade.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Código de erro:  SqlColumnNameMismatchByCaseSensitive

- **Mensagem**: `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Código de erro:  SqlBatchWriteTimeout

- **Mensagem**: `Timeouts in SQL write operation.`

- **Causa**: Pode ser uma falha transitória do Banco de Dados SQL.

- **Recomendação**: tente novamente. Se o problema for reproduzido, entre em contato com o suporte do SQL do Azure.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Código de erro:  SqlBatchWriteTransactionFailed

- **Mensagem**: `SQL transaction commits failed`

- **Causa**: Se os detalhes da exceção informarem constantemente o tempo limite da transação, a latência de rede entre o runtime de integração e o banco de dados será maior que o limite padrão como 30 segundos.

- **Recomendação**:  Atualize a cadeia de conexão do serviço vinculado do SQL com o valor “tempo limite de conexão” igual a 120 ou superior e execute a atividade novamente.

- **Causa**: Se os detalhes da exceção intermitentemente informarem que o sqlconnection está quebrado, pode haver uma falha de rede transitória ou um problema do lado do Banco de Dados SQL

- **Recomendação**: repita a atividade e examine as métricas do lado do banco de dados SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Código de erro:  SqlBulkCopyInvalidColumnLength

- **Mensagem**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: A cópia em massa do SQL falhou porque recebeu um comprimento de coluna inválido do cliente bcp.

- **Recomendação**:  Para identificar em qual linha está o problema, habilite o recurso de tolerância a falhas na atividade de cópia, que pode redirecionar as linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Documento de referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Código de erro:  SqlConnectionIsClosed

- **Mensagem**: `The connection is closed by SQL Database.`

- **Causa**: A conexão SQL é fechada pelo Banco de Dados SQL quando a execução simultânea alta e o servidor terminarem a conexão.

- **Recomendação**:  O servidor remoto encerrou a conexão SQL. Repetir. Se o problema for reproduzido, entre em contato com o suporte do SQL do Azure.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Código de erro:  SqlCreateTableFailedUnsupportedType

- **Mensagem**: `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Mensagem de erro: Falha na conversão de uma cadeia de caracteres em um uniqueidentifier

- **Sintomas**: quando você copia dados da fonte de dados tabulares (como SQL Server) para o Azure Synapse Analytics usando cópia preparada e polybase, você clica no seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Causa**: o polybase do Azure Synapse Analytics não pode converter uma cadeia de caracteres vazia em GUID.

- **Resolução:** Em um coletor de atividade de cópia, nas configurações do PolyBase, defina a opção "**usar padrão de tipo**" como false.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Mensagem de erro: Tipo de dados esperado: DECIMAL (x,x), valor incorreto

- **Sintomas**: quando você copia dados da fonte de dados tabulares (como SQL Server) para o Azure Synapse Analytics usando cópia preparada e polybase, você clica no seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Causa**: o polybase do Azure Synapse Analytics não pode inserir uma cadeia de caracteres vazia (valor nulo) em uma coluna decimal.

- **Resolução:** Em um coletor de atividade de cópia, nas configurações do PolyBase, defina a opção "**usar padrão de tipo**" como false.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Mensagem de erro: mensagem de exceção Java: HdfsBridge:: CreateRecordReader

- **Sintomas**: você copia dados para o Azure Synapse Analytics usando o polybase e clica no seguinte erro:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Causa**: Uma causa possível é que o esquema (largura da coluna total) é grande demais (maior que 1 MB). Verifique o esquema da tabela de destino do Azure Synapse Analytics adicionando o tamanho de todas as colunas:

    - Int -> 4 bytes
    - Bigint -> 8 bytes
    - Varchar(n),char(n),binary(n), varbinary(n) -> n bytes
    - Nvarchar(n), nchar(n) -> n*2 bytes
    - Date -> 6 bytes
    - Datetime/(2), smalldatetime -> 16 bytes
    - Datetimeoffset -> 20 bytes
    - Decimal -> 19 bytes
    - Float -> 8 bytes
    - Money -> 8 bytes
    - Smallmoney -> 4 bytes
    - Real -> 4 bytes
    - Smallint -> 2 bytes
    - Time -> 12 bytes
    - Tinyint -> 1 byte

- **Resolução:** Reduza a largura da coluna para que seja menor que 1 MB

- Ou use a abordagem bulk insert desabilitando o Polybase


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Mensagem de erro: A condição especificada usando cabeçalhos condicionais HTTP não foi atendida

- **Sintomas**: você usa a consulta SQL para efetuar pull de dados do Azure Synapse Analytics e clica no seguinte erro:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa**: problema de visita do Azure Synapse Analytics consultando a tabela externa no armazenamento do Azure.

- **Resolução:** Execute a mesma consulta no SSMS e verifique se você vê o mesmo resultado. Em caso afirmativo, abra um tíquete de suporte para o Azure Synapse Analytics e forneça o nome do servidor e do banco de dados do Azure Synapse Analytics para solucionar outros problemas.
            

### <a name="low-performance-when-load-data-into-azure-sql"></a>Baixo desempenho ao carregar dados no SQL do Azure

- **Sintomas**: a cópia de dados no SQL do Azure torna-se lenta.

- **Causa**: a causa raiz do problema é disparada principalmente pelo afunilamento do lado do Azure SQL. Veja a seguir algumas causas possíveis:

    1. A camada de banco de BD do Azure não é alta o suficiente.

    1. O uso de DTU de BD do Azure está próximo de 100%. Você pode [monitorar o desempenho](https://docs.microsoft.com/azure/azure-sql/database/monitor-tune-overview) e considerar a atualização da camada de BD.

    1. Os índices não estão definidos corretamente. Remova todos os índices antes de carregar os dados e recrie-os após a conclusão da carga.

    1. WriteBatchSize não é grande o suficiente para ajustar o tamanho da linha do esquema. Tente aumentar a propriedade para o problema.

    1. Em vez da inserção em massa, o procedimento armazenado está sendo usado, o que deve ter um desempenho pior. 

- **Resolução**: consulte o TSG para obter o [desempenho da atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting)


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>O nível de desempenho é baixo e leva a uma falha de cópia

- **Sintomas**: a mensagem de erro abaixo ocorreu ao copiar dados para o SQL do Azure: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Causa**: o SQL do Azure S1 está sendo usado, que atingiu os limites de e/s nesse caso.

- **Resolução**: Atualize o nível de desempenho do Azure SQL para corrigir o problema. 


### <a name="sql-table-cannot-be-found"></a>Não é possível encontrar a tabela SQL 

- **Sintomas**: ocorreu um erro ao copiar dados de híbrido para uma tabela de SQL Server local:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Causa**: a conta SQL atual não tem permissão suficiente para executar solicitações emitidas pelo .net SqlBulkCopy. WriteToServer.

- **Resolução**: mude para uma conta do SQL mais privilegiada.


### <a name="string-or-binary-data-would-be-truncated"></a>A cadeia de caracteres ou os dados binários seriam truncados

- **Sintomas**: ocorreu um erro ao copiar dados para a tabela local/SQL Server do Azure: 

- **Causa**: a definição do esquema de tabela do CX SQL tem uma ou mais colunas com menos comprimento que a expectativa.

- **Resolução**: tente as etapas a seguir para corrigir o problema:

    1. Aplique a [tolerância a falhas](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance), especialmente "redirectIncompatibleRowSettings" para solucionar quais linhas têm o problema.

    1. Verifique novamente os dados redirecionados com o comprimento da coluna do esquema de tabela SQL para ver quais colunas precisam ser atualizadas.

    1. Atualize o esquema da tabela adequadamente.


## <a name="delimited-text-format"></a>Formato de texto delimitado

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Código de erro:  DelimitedTextColumnNameNotAllowNull

- **Mensagem**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa**: Quando você definir “firstRowAsHeader” na atividade, a primeira linha será usada como nome da coluna. Esse erro significa que a primeira linha contém um valor vazio. Por exemplo: ' ColumnA, coluna b '.

- **Recomendação**:  Verifique a primeira linha e corrija o valor se houver um valor vazio.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Código de erro:  DelimitedTextMoreColumnsThanDefined

- **Mensagem**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Causa**: a contagem de colunas da linha problemática é maior que a contagem de colunas da primeira linha. Pode ser causado por problemas de dados ou configurações incorretas de caractere delimitador de coluna/caractere de cotação.

- **Recomendação**: obter a contagem de linhas na mensagem de erro, verificar a coluna da linha e corrigir os dados.

- **Causa**: se a contagem de colunas esperada for "1" na mensagem de erro, talvez você tenha especificado configurações incorretas de formato ou compactação. Portanto, o ADF analisou seus arquivos incorretamente.

- **Recomendação**:  Verifique as configurações de formato para garantir que ele corresponda aos seus arquivos de origem.

- **Causa**: Se sua origem for uma pasta, é possível que os arquivos na pasta especificada tenham um esquema diferente.

- **Recomendação**:  Verifique se os arquivos na pasta especificada têm esquema idêntico.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Código de erro:  DelimitedTextIncorrectRowDelimiter

- **Mensagem**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Código de erro:  DelimitedTextTooLargeColumnCount

- **Mensagem**: `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Código de erro:  DelimitedTextInvalidSettings

- **Mensagem**: `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Código de erro:  DynamicsCreateServiceClientError

- **Mensagem**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Causa**: Esse é um problema transitório no lado do servidor do Dynamics.

- **Recomendação**:  Executar novamente o pipeline. Se a falha persistir, tente reduzir o paralelismo. Se isso não resolver, entre em contato com o suporte do Dynamics.


### <a name="columns-are-missing-when-previewingimporting-schema"></a>Colunas ausentes ao Visualizar/importar esquema

- **Sintomas**: algumas das colunas ficam ausentes ao importar o esquema ou a visualização de dados. Mensagem de erro: `The valid structure information (column name and type) are required for Dynamics source.`

- **Causa**: esse problema é basicamente por design, pois o ADF não é capaz de mostrar colunas que não têm valor nos primeiros 10 registros. Certifique-se de que as colunas que você adicionou estão com o formato correto. 

- **Recomendação**: adicione manualmente as colunas na guia mapeamento.


## <a name="excel-format"></a>Formato do Excel

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Tempo limite ou desempenho lento ao analisar arquivo grande do Excel

- **Sintomas**:

    1. Quando você cria o conjunto de dados do Excel e importa o esquema de conexão/armazenamento, visualização de planilhas, lista ou atualização de pastas de trabalho, você pode atingir um erro de tempo limite se o arquivo do Excel for grande em tamanho.

    1. Quando você usa a atividade de cópia para copiar dados de um arquivo grande do Excel (>= 100 MB) para outro armazenamento de dados, você pode enfrentar um problema de desempenho ou OOM lento.

- **Causa**: 

    1. Para operações como importação de esquema, visualização de dados e listagem de planilhas no conjunto de dados do Excel, o tempo limite é 100s e estático. Para grandes arquivos do Excel, essas operações podem não ser concluídas dentro do valor de tempo limite.

    2. A atividade de cópia do ADF lê o arquivo inteiro do Excel na memória e, em seguida, localiza a planilha e as células especificadas para ler os dados. Esse comportamento ocorre devido ao uso do ADF do SDK subjacente.

- **Resolução**: 

    1. Para importar o esquema, você pode gerar um arquivo de exemplo menor, que é um subconjunto do arquivo original, e escolher "importar esquema do arquivo de exemplo" em vez de "importar esquema da conexão/armazenamento".

    2. Para a planilha de listagem, no menu suspenso da planilha, você pode clicar em "Editar" e inserir o nome/índice da planilha em vez disso.

    3. Para copiar arquivos grandes do Excel (>100MB) em outro repositório, você pode usar a origem do Excel de fluxo de dados que usa streaming de esporte e executar melhor.


## <a name="hdinsight"></a>HDInsight

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>Erro de SSL quando o serviço vinculado do ADF está usando o cluster do HDInsight ESP

- **Mensagem**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **Causa**: o problema provavelmente está relacionado ao repositório de confiança do sistema.

- **Resolução**: você pode navegar até o caminho **Microsoft Integration RUNTIME\4.0\SHARED\ODBC Drivers\Microsoft Hive ODBC Driver\lib** e abrir DriverConfiguration64.exe para alterar a configuração.

    ![Desmarque usar repositório de confiança do sistema](./media/connector-troubleshoot-guide/system-trust-store-setting.png)


## <a name="json-format"></a>Formato JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Código de erro:  JsonInvalidArrayPathDefinition

- **Mensagem**: `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Código de erro:  JsonEmptyJObjectData

- **Mensagem**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Código de erro:  JsonNullValueInPathDefinition

- **Mensagem**: `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Código de erro:  JsonUnsupportedHierarchicalComplexValue

- **Mensagem**: `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Código de erro:  JsonConflictPartitionDiscoverySchema

- **Mensagem**: `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Código de erro:  JsonInvalidDataFormat

- **Mensagem**: `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Código de erro:  JsonInvalidDataMixedArrayAndObject

- **Mensagem**: `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Código de erro: ArgumentOutOfRangeException

- **Mensagem**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Causa**: no ADF, os valores de data e hora têm suporte no intervalo de 0001-01-01 00:00:00 a 9999-12-31 23:59:59. No entanto, o Oracle dá suporte ao intervalo mais amplo de valor DateTime (como o século BC ou mín/s>59), o que leva a uma falha no ADF.

- **Recomendação**: 

    Execute `select dump(<column name>)` para verificar se o valor no Oracle está no intervalo do ADF. 

    Se você quiser saber a sequência de bytes no resultado, verifique https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle .


## <a name="parquet-format"></a>Formato Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Código de erro:  ParquetJavaInvocationException

- **Mensagem**: `An error occurred when invoking java, message: %javaException;.`

- **Causa**: Quando a mensagem de erro contém “java.lang.OutOfMemory”, “Java heap space” e “doubleCapacity”, geralmente é um problema de gerenciamento de memória na versão antiga do runtime de integração.

- **Recomendação**: se você estiver usando Integration Runtime de hospedagem interna e a versão for anterior à 3.20.7159.1, é recomendável atualizar para a versão mais recente.

- **Causa**: Quando a mensagem de erro contém “java.lang.OutOfMemory'” o runtime de integração não tem recursos suficientes para processar os arquivos.

- **Recomendação**:  Limite as execuções simultâneas no runtime de integração. Para runtime de integração auto-hospedada, escale verticalmente para um computador avançado com memória igual ou maior que 8 GB.

- **Causa**: Quando a mensagem de erro contém “NullPointerReference”, é possível que seja um erro transitório.

- **Recomendação**: tente novamente. Se o problema persistir, entre em contato com o suporte.


### <a name="error-code--parquetinvalidfile"></a>Código de erro:  ParquetInvalidFile

- **Mensagem**: `File is not a valid Parquet file.`

- **Causa**: Problema de arquivo parquet.

- **Recomendação**: Verifique se a entrada é um arquivo parquet válido.


### <a name="error-code--parquetnotsupportedtype"></a>Código de erro:  ParquetNotSupportedType

- **Mensagem**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa**: o formato parquet não tem suporte no Azure data Factory.

- **Recomendação**:  Verifique bem os dados de origem. Consulte o documento: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Código de erro:  ParquetMissedDecimalPrecisionScale

- **Mensagem**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa**: Tentar analisar a precisão e a escala do número, mas nenhuma informação é fornecida.

- **Recomendação**:  “Source” não retorna a precisão e a escala corretas. Verifique a precisão e a escala da coluna do problema.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Código de erro:  ParquetInvalidDecimalPrecisionScale

- **Mensagem**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa**: O esquema é inválido.

- **Recomendação**:  Verifique a precisão e a escala da coluna do problema.


### <a name="error-code--parquetcolumnnotfound"></a>Código de erro:  ParquetColumnNotFound

- **Mensagem**: `Column %column; does not exist in Parquet file.`

- **Causa**: O esquema de origem é incompatível com o esquema de coletor.

- **Recomendação**:  Verifique os “mapeamentos” em “atividade”. Verifique se a coluna de origem pode ser mapeada para a coluna de coletor correta.


### <a name="error-code--parquetinvaliddataformat"></a>Código de erro:  ParquetInvalidDataFormat

- **Mensagem**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: Os dados não podem ser convertidos no tipo especificado em mappings.source

- **Recomendação**:  Verifique os dados de origem ou especifique o tipo de dados correto para essa coluna no mapeamento de coluna da atividade de cópia. Consulte o documento: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Código de erro:  ParquetDataCountNotMatchColumnCount

- **Mensagem**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa**: Contagem de colunas de origem e número de colunas de coletor é incompatível

- **Recomendação**:  Verifique bem se a contagem de colunas de origem é igual à contagem de colunas do coletor em “mapeamento”.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Código de erro:  ParquetDataTypeNotMatchColumnType

- **Mensagem**: O tipo de dados % srcType; não corresponde ao tipo de coluna dado %dstType; na coluna “%columnIndex;”.

- **Causa**: Os dados da origem não podem ser convertidos em tipos definidos no coletor

- **Recomendação**: especifique um tipo correto no Mapping. Sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Código de erro:  ParquetBridgeInvalidData

- **Mensagem**: `%message;`

- **Causa**: Valor de dados acima da limitação

- **Recomendação**: tente novamente. Se o problema persistir, entre em contato conosco.


### <a name="error-code--parquetunsupportedinterpretation"></a>Código de erro:  ParquetUnsupportedInterpretation

- **Mensagem**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Causa**: Cenário sem suporte

- **Recomendação**:  “ParquetInterpretFor” não deve ser “sparkSql”.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Código de erro:  ParquetUnsupportFileLevelCompressionOption

- **Mensagem**: `File level compression is not supported for Parquet.`

- **Causa**: Cenário sem suporte

- **Recomendação**:  Remova “CompressionType” do payload.


### <a name="error-code--usererrorjniexception"></a>Código de erro: UserErrorJniException

- **Mensagem**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Causa**: a JVM não pode ser criada porque alguns argumentos (globais) ilegais estão definidos.

- **Recomendação**: faça logon no computador que hospeda **cada nó** do seu ir hospedado automaticamente. Verifique se a variável do sistema está definida corretamente da seguinte maneira: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Reinicie todos os nós IR e, em seguida, execute novamente o pipeline.


### <a name="arithmetic-overflow"></a>Estouro aritmético

- **Sintomas**: mensagem de erro ao copiar arquivos parquet: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Causa**: atualmente, somente o decimal da precisão <= 38 e o comprimento da parte inteira <= 20 tem suporte ao copiar arquivos do Oracle para o parquet. 

- **Resolução**: você pode converter colunas com esse problema em varchar2 como uma solução alternativa.


### <a name="no-enum-constant"></a>Nenhuma constante de enumeração

- **Sintomas**: mensagem de erro ao copiar dados para o formato parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , ou: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Causa**: 

    O problema pode ser causado por espaços em branco ou caracteres sem suporte (como,; {} () \n\t =) no nome da coluna, já que parquet não dá suporte a tal formato. 

    Por exemplo, o nome da coluna como *contoso (Test)* analisará o tipo entre colchetes do [código](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . O erro será gerado, pois não há tal tipo de "teste".

    Para verificar os tipos com suporte, você pode verificá-los [aqui](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Resolução**: 

    1. Verifique se há espaços em branco no nome da coluna do coletor.

    1. Verifique se a primeira linha com espaços em branco é usada como nome da coluna.

    1. Verifique se o tipo Originaltype tem suporte ou não. Tente evitar esses símbolos especiais `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="unexpected-network-response-from-rest-connector"></a>Resposta de rede inesperada do conector REST

- **Sintomas**: o ponto de extremidade às vezes recebe resposta inesperada (400/401/403/500) do conector REST.

- **Causa**: o conector de origem REST usa URL e método HTTP/cabeçalho/corpo de um serviço vinculado/conjunto de conteúdo/fonte de cópia como parâmetros ao construir uma solicitação HTTP. O problema é provavelmente causado por alguns erros em um ou mais parâmetros especificados.

- **Resolução**: 
    - Use ' ondulação ' na janela cmd para verificar se o parâmetro é a causa ou não (os cabeçalhos **Accept** e **User-Agent** devem ser sempre incluídos):
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      Se o comando retornar a mesma resposta inesperada, corrija os parâmetros acima com ' ondulação ' até que ele retorne a resposta esperada. 

      Além disso, você pode usar ' enrolation--Help ' para uso mais avançado do comando.

    - Se apenas o conector REST do ADF retornar uma resposta inesperada, entre em contato com o suporte da Microsoft para obter mais soluções de problemas.
    
    - Observe que a ' ondulação ' pode não ser adequada para reproduzir o problema de validação do certificado SSL. Em alguns cenários, o comando ' ondulação ' foi executado com êxito sem alcançar nenhum problema de validação de certificado SSL. Mas quando a mesma URL é executada no navegador, nenhum certificado SSL é realmente retornado em primeiro lugar para o cliente estabelecer uma relação de confiança com o servidor.

      Ferramentas como o **postmaster** e o **Fiddler** são recomendadas para o caso acima.


## <a name="sftp"></a>SFTP

### <a name="invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Credencial SFTP inválida fornecida para o tipo de autenticação ' SSHPublicKey '

- **Sintomas**: a autenticação de chave pública SSH está sendo usada enquanto uma CREDENCIAl SFTP inválida é fornecida para o tipo de autenticação ' SshPublicKey '.

- **Causa**: esse erro pode ser causado por três motivos possíveis:

    1. O conteúdo da chave privada é buscado do AKV/SDK, mas não está codificado corretamente.

    1. Formato de conteúdo de chave incorreto escolhido.

    1. Conteúdo de chave privada ou credencial inválida.

- **Resolução**: 

    1. Para a **causa 1**:

       Se o conteúdo da chave privada for de AKV e o arquivo de chave original puder funcionar se o cliente carregá-lo diretamente no serviço vinculado do SFTP

       Consulte https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication o conteúdo de PrivateKey é um conteúdo de chave privada SSH codificado em base64.

       Codifique **todo o conteúdo do arquivo de chave privada original** com codificação Base64 e armazene a cadeia de caracteres codificada em akv. O arquivo de chave privada original é aquele que pode funcionar no serviço vinculado do SFTP se você clicar em carregar do arquivo.

       Aqui estão alguns exemplos usados para gerar a cadeia de caracteres:

       - Usar código C#:
       ```
       byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
       string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
       ```

       - Use o código Python:
       ```
       import base64
       rfd = open(r'{Private Key Path}', 'rb')
       keyContent = rfd.read()
       rfd.close()
       print base64.b64encode(Key Content)
       ```

       - Usar ferramenta de conversão de Base64 de terceiros

         Ferramentas como https://www.base64encode.org/ são recomendadas.

    1. Para a **causa 2**:

       Se a chave privada SSH de formato PKCS # 8 estiver sendo usada

       PKCS # 8 Format chave privada SSH (começar com "-----começar a chave privada CRIPTOGRAFAda-----") não tem suporte no momento para acessar o servidor SFTP no ADF. 

       Execute os comandos abaixo para converter a chave para o formato de chave SSH tradicional (comece com "-----iniciar a chave privada RSA-----"):

       ```
       openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
       chmod 600 traditional_format_key_file
       ssh-keygen -f traditional_format_key_file -p
       ```
    1. Para a **causa 3**:

       Verifique novamente com ferramentas como WinSCP para ver se o arquivo de chave ou a senha está correta.


### <a name="incorrect-linked-service-type-is-used"></a>O tipo de serviço vinculado incorreto é usado

- **Sintomas**: o servidor FTP/SFTP não pode ser acessado.

- **Causa**: o tipo de serviço vinculado incorreto é usado para o servidor FTP ou SFTP, como usar o serviço vinculado de FTP para se conectar a um servidor SFTP ou vice-versa.

- **Resolução**: Verifique a porta do servidor de destino. Por padrão, o FTP usa a porta 21 e o SFTP usa a porta 22.


### <a name="sftp-copy-activity-failed"></a>Falha na atividade de cópia de SFTP

- **Sintomas**: código de erro: UserErrorInvalidColumnMappingColumnNotFound. Mensagem de erro: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **Causa**: a origem não inclui uma coluna chamada "AccMngr".

- **Resolução**: Verifique novamente como o conjunto de acordo está configurado mapeando a coluna do conjunto de banco de de destino para confirmar se há uma coluna "AccMngr".


### <a name="sftp-server-connection-throttling"></a>Limitação de conexão do servidor SFTP

- **Sintomas**: a resposta do servidor não contém a identificação do protocolo SSH e falhou ao copiar.

- **Causa**: o ADF criará várias conexões para baixar do servidor SFTP em paralelo e, às vezes, atingirá a limitação do servidor SFTP. Praticamente, um servidor diferente retornará um erro diferente quando a limitação de acesso for atingida.

- **Resolução**: 

    Especifique a conexão simultânea máxima do conjunto de conjuntos SFTP como 1 e execute a cópia novamente. Se for bem-sucedido, poderemos ter certeza de que a limitação é a causa.

    Se você quiser promover a taxa de transferência baixa, entre em contato com o administrador do SFTP para aumentar o limite de contagem de conexões simultâneas ou adicione o IP abaixo à lista de permissões:

    - Se você estiver usando o IR gerenciado, adicione [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).
      Ou você pode instalar o IR auto-hospedado se não quiser adicionar uma lista grande de intervalos de IP à lista de permissões do servidor SFTP.

    - Se você estiver usando o IR de hospedagem interna, adicione o IP do computador que instalou o SHIR à lista de permissões.


### <a name="error-code-sftprenameoperationfail"></a>Código de erro: SftpRenameOperationFail

- **Sintomas**: o pipeline não pôde copiar dados do blob para SFTP com o seguinte erro: `Operation on target Copy_5xe failed: Failure happened on 'Sink' side. ErrorCode=SftpRenameOperationFail,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException` .

- **Causa**: a opção useTempFileRename foi definida como true ao copiar os dados. Isso permite que o processo use arquivos temporários. O erro será disparado se um ou mais arquivos temporários forem excluídos antes de os dados completos serem copiados.

- **Resolução**: defina a opção de UseTempFileName como false.


## <a name="general-copy-activity-error"></a>Erro geral de atividade de cópia

### <a name="error-code--jrenotfound"></a>Código de erro:  JreNotFound

- **Mensagem**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa**: O runtime de integração auto-hospedada não consegue localizar o Java Runtime. O Java Runtime é necessário para ler a origem específica.

- **Recomendação**:  Verifique seu ambiente de runtime de integração, o documento de referência: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Código de erro:  WildcardPathSinkNotSupported

- **Mensagem**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa**: O conjunto de dados do coletor não oferece suporte a caractere curinga.

- **Recomendação**:  Verifique o conjunto de dados do coletor e corrija o caminho sem o valor de curinga.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Código de erro:  MappingInvalidPropertyWithEmptyValue

- **Mensagem**: `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Código de erro:  MappingInvalidPropertyWithNamePathAndOrdinal

- **Mensagem**: `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Código de erro:  MappingDuplicatedOrdinal

- **Mensagem**: `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Código de erro:  MappingInvalidOrdinalForSinkColumn

- **Mensagem**: `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página de perguntas e respostas da Microsoft](/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
