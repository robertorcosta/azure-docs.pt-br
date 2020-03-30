---
title: Ingerir dados usando a biblioteca do Node do Azure Data Explorer
description: Neste artigo, você aprende como ingerir dados (carregar) no Azure Data Explorer usando o Node.js.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 19da42437cfe1d7b63dfed4bd2b30716d691a0e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66494491"
---
# <a name="ingest-data-using-the-azure-data-explorer-node-library"></a>Ingerir dados usando a biblioteca do Node do Azure Data Explorer

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Data Explorer do Azure fornece duas bibliotecas de cliente para Node: uma [biblioteca de ingestão](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) e [uma biblioteca de dados](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). Essas bibliotecas permitem a inclusão de dados (carga) em um cluster e dados de consulta do seu código. Neste artigo, você primeiro cria uma tabela e mapeamento de dados em um cluster de teste. Você, em seguida, enfileira ao cluster e valida os resultados.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Além de uma assinatura do Azure, você precisa do seguinte para concluir este artigo:

* [Um cluster de teste e banco de dados](create-cluster-database-portal.md)

* [Node.js](https://nodejs.org/en/download/) instalado no seu computador de desenvolvimento

## <a name="install-the-data-and-ingest-libraries"></a>Instalar os dados e bibliotecas de ingestão

Instalar *azure-kusto-ingest* e *azure-kusto-data*

```bash
npm i azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Adicionar instruções de importação e constantes

Importar classes das bibliotecas

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
Para autenticar um aplicativo, o Azure Data Explorer usa sua ID de locatário do Azure Active Directory. Para localizar sua ID de locatário, siga [Localizar sua ID de locatário do Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).

Defina os valores de `authorityId`, `kustoUri`, `kustoIngestUri` e `kustoDatabase` antes de executar esse código.

```javascript
const cluster = "MyCluster";
const region = "westus";
const authorityId = "microsoft.com";
const kustoUri = `https://${cluster}.${region}.kusto.windows.net:443`;
const kustoIngestUri = `https://ingest-${cluster}.${region}.kusto.windows.net:443`;
const kustoDatabase  = "Weather";
```

Agora, construa a cadeia de caracteres de conexão. Este exemplo usa a autenticação do dispositivo para acessar o cluster. Você também pode usar o certificado de aplicativo, a chave de aplicativo, o usuário e a senha do Azure Active Directory.

Criar a tabela de destino e o mapeamento em uma etapa posterior.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>Definir informações de arquivo de origem

Importar classes adicionais e definir constantes para o arquivo de fonte de dados. Este exemplo usa um arquivo de exemplo hospedado no armazenamento de BLOBs do Azure. O conjunto de dados da amostra **StormEvents** contém dados relacionados ao clima dos [Centros Nacionais de Informações Ambientais](https://www.ncdc.noaa.gov/stormevents/).

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>Criar uma tabela em seu cluster de teste

Crie uma tabela que corresponde ao esquema dos dados no arquivo `StormEvents.csv`. Quando esse código é executado, ele retorna uma mensagem como a seguinte: *Para entrar, use um navegador da web para abrir a páginahttps://microsoft.com/devicelogin e digite o código XXXXXXXXX para autenticar*. Siga as etapas para entrar e retorne para executar o próximo bloco de código. Blocos de código subsequente que compõem uma conexão exigirão que você entre novamente.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="define-ingestion-mapping"></a>Definir mapeamento de ingestão

Mapear os dados JSON de entrada para os nomes de colunas e tipos de dados usados ao criar a tabela.

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="queue-a-message-for-ingestion"></a>Enfileirar uma mensagem para ingestão

Enfileirar uma mensagem para extrair dados do armazenamento de BLOBs e ingerir esses dados no Data Explorer do Azure.

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Validar que a tabela contém dados

Validar que os dados foram ingeridos pela tabela. Aguardar cinco a dez minutos para a ingestão na fila agendar a ingestão e carregar os dados no Data Explorer do Azure. Em seguida, execute o código a seguir para obter a contagem de registros na tabela `StormEvents`.

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>Executar consultas de solução de problemas

Faça login [https://dataexplorer.azure.com](https://dataexplorer.azure.com) e conecte-se ao seu cluster. Execute o seguinte comando no banco de dados para ver se houve alguma falha de ingestão nas últimas quatro horas. Substitua o nome do banco de dados antes da execução.
    
```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Execute o seguinte comando para exibir o status de todas as operações de ingestão nas últimas quatro horas. Substitua o nome do banco de dados antes da execução.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou. Caso contrário, execute o comando a seguir no seu banco de dados para limpar a tabela `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Próximas etapas

* [Gravar consultas](write-queries.md)
