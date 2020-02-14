---
title: Ingerir dados usando o SDK do .NET Standard do Azure Data Explorer (Versão prévia)
description: Neste artigo, você aprenderá a ingerir (carregar) dados no Azure Data Explorer usando o SDK do .NET Standard.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1fb1301ae7e0cdff36f3771a44769c8bf9cc9c62
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187927"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Ingerir dados usando o SDK do .NET Standard do Azure Data Explorer (Versão prévia)

O Azure Data Explorer (ADX) é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O ADX fornece duas bibliotecas de cliente para .NET Standard: uma [biblioteca de ingestão](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) e [uma biblioteca de dados](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Essas bibliotecas permitem a inclusão de dados (carga) em um cluster e dados de consulta do seu código. Neste artigo, você primeiro cria uma tabela e um mapeamento de dados em um cluster de teste. Depois, você enfileira uma ingestão no cluster e valida os resultados.

## <a name="prerequisites"></a>Prerequisites

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

* [Um cluster de teste e um banco de dados](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Instalar a biblioteca de ingestão

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Autenticação

Para autenticar um aplicativo, o Azure Data Explorer usa seu ID de locatário do AAD. Para encontrar seu ID de locatário, use a seguinte URL, substituindo seu domínio por *YourDomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por exemplo, se o seu domínio for *contoso.com*, a URL será: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Clique nesta URL para ver os resultados; a primeira linha é a seguinte. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

A ID do locatário neste caso é `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

Este exemplo usa um usuário do AAD e a senha de autenticação para acessar o cluster. Você também pode usar o certificado de aplicativo AAD e a chave de aplicativo AAD. Defina os valores de `tenantId`, `user`, e `password` antes de executar esse código.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Construir a cadeia de conexão
Agora, construa a cadeia de caracteres de conexão. Criar a tabela de destino e o mapeamento em uma etapa posterior.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Definir informações de arquivo de origem

Defina o caminho até o arquivo de origem. Este exemplo usa um arquivo de exemplo hospedado no armazenamento de BLOBs do Azure. O conjunto de dados de amostra **StormEvents** contém dados relacionados ao clima dos [Centros Nacionais de Informações Ambientais](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Criar uma tabela em seu cluster de teste
Crie uma tabela chamada `StormEvents` que corresponde ao esquema dos dados no arquivo `StormEvents.csv`.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Definir mapeamento de ingestão

Mapear os dados CSV de entrada para os nomes de colunas usados ao criar a tabela.
Provisionar um [objeto de mapeamento de coluna CSV](/azure/kusto/management/create-ingestion-mapping-command) nessa tabela

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EpisodeId", Ordinal = 2 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "EventType", Ordinal = 5 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 15 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 21 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Enfileirar uma mensagem para ingestão

Enfileirar uma mensagem para extrair dados do armazenamento de blob e ingerir esses dados no ADX.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Validar os dados ingeridos na tabela

Aguardar cinco a dez minutos para a ingestão na fila agendar a ingestão e carregar os dados no ADX. Em seguida, execute o código a seguir para obter a contagem de registros na tabela `StormEvents`.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>Executar consultas de solução de problemas

Conectar [https://dataexplorer.azure.com](https://dataexplorer.azure.com) e conectar ao seu cluster. Execute o seguinte comando no banco de dados para ver se houve alguma falha de ingestão nas últimas quatro horas. Substitua o nome do banco de dados antes da execução.

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

## <a name="clean-up-resources"></a>Limpar os recursos

Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou. Caso contrário, execute o comando a seguir no seu banco de dados para limpar a tabela `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Próximas etapas

* [Gravar consultas](write-queries.md)
