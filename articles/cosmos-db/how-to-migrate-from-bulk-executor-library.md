---
title: Migre da biblioteca de executores em massa para o suporte em massa no Azure Cosmos DB .NET V3 SDK
description: Saiba como migrar seu aplicativo usando a biblioteca de executores em massa para o suporte em massa no Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: maquaran
ms.openlocfilehash: 820a5398d84122659b1676b7d5722bce08b1837d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755982"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migre da biblioteca de executores em massa para o suporte em massa no Azure Cosmos DB .NET V3 SDK

Este artigo descreve as etapas necessárias para migrar o código de um aplicativo existente que usa a [biblioteca de executores](bulk-executor-dot-net.md) em massa .NET para o recurso de suporte em [massa](tutorial-sql-api-dotnet-bulk-import.md) na versão mais recente do .NET SDK.

## <a name="enable-bulk-support"></a>Habilite o suporte em massa

Habilite o `CosmosClient` suporte em massa na instância através da configuração [AllowBulkExecution:](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Criar tarefas para cada operação

O suporte em massa no .NET SDK funciona aproveitando a [Biblioteca Paralela de Tarefas](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) e agrupando operações que ocorrem simultaneamente. 

Não há um único método que leve sua lista de documentos ou operações como parâmetro de entrada, mas, em vez disso, você precisa criar uma Tarefa para cada operação que deseja executar em massa.

Por exemplo, se sua entrada inicial for uma lista de itens onde cada item tem o seguinte esquema:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Se você quiser fazer importação em massa (semelhante ao uso de BulkExecutor.BulkImportAsync), você precisa ter chamadas simultâneas para `CreateItemAsync` cada valor de item. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Se você quiser fazer *atualização* em massa (semelhante ao uso [do BulkExecutor.BulkUpdateAsync),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)você precisa ter chamadas simultâneas para o `ReplaceItemAsync` método após atualizar o valor do item. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

E se você quiser fazer *a exclusão* em massa (semelhante ao uso [bulkExecutor.BulkDeleteAsync),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)você precisa ter chamadas simultâneas para, `DeleteItemAsync`com a `id` chave e partição de cada item. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Estado do resultado da tarefa de captura

Nos exemplos de código anteriores, você criou uma lista simultânea `CaptureOperationResponse` de tarefas e chamou o método em cada uma dessas tarefas. Este método é uma extensão que nos permite manter um *esquema de resposta semelhante* ao BulkExecutor, capturando quaisquer erros e rastreando o uso das unidades de [solicitação](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Onde `OperationResponse` o é declarado como:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Executar operações simultaneamente

Depois que a lista de tarefas for definida, aguarde até que todas estejam concluídas. Você pode acompanhar a conclusão das tarefas definindo o escopo de sua operação em massa, conforme mostrado no seguinte trecho de código:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Estatísticas de captura

O código anterior aguarda até que todas as operações sejam concluídas e calcula as estatísticas necessárias. Essas estatísticas são semelhantes às da [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)da biblioteca executora em massa .

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

O `BulkOperationResponse` contém:

1. O tempo total gasto para processar a lista de operações através de suporte em massa.
1. O número de operações bem sucedidas.
1. O total de unidades de solicitação consumidas.
1. Se houver falhas, ele exibe uma lista de tuplas que contêm a exceção e o item associado para fins de registro e identificação.

## <a name="retry-configuration"></a>Configuração de retentativa

A biblioteca de executores em `MaxRetryWaitTimeInSeconds` `MaxRetryAttemptsOnThrottledRequests` massa tinha [orientação](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) que mencionava para definir o e de [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) para `0` delegar o controle à biblioteca.

Para suporte em massa no .NET SDK, não há comportamento oculto. Você pode configurar as opções de repetição diretamente através do [CosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) e [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> Nos casos em que as unidades de solicitação provisionadas são muito inferiores ao esperado com base na quantidade de dados, você pode considerar defini-los como valores elevados. A operação em massa levará mais tempo, mas tem uma chance maior de ter sucesso total devido às tentativas mais altas.

## <a name="performance-improvements"></a>Melhorias de desempenho

Como em outras operações com o .NET SDK, o uso das APIs de fluxo resulta em melhor desempenho e evita qualquer serialização desnecessária. 

O uso de APIs de fluxo só é possível se a natureza dos dados que você usa corresponder à de um fluxo de bytes (por exemplo, fluxos de arquivos). Nesses casos, usar `CreateItemStreamAsync` `ReplaceItemStreamAsync`os `DeleteItemStreamAsync` métodos ou `ResponseMessage` trabalhar com `ItemResponse`(em vez de ) aumenta o throughput que pode ser alcançado.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as versões do .NET SDK, consulte o artigo [do Azure Cosmos DB SDK.](sql-api-sdk-dotnet.md)
* Obtenha o código fonte de [migração](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) completo do GitHub.
* [Amostras adicionais a granel no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
