---
title: Monitore o status e os resultados do indexador
titleSuffix: Azure Cognitive Search
description: Monitore o status, o progresso e os resultados dos indexadores de pesquisa cognitiva do Azure no portal Azure, usando a API REST ou o .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112984"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Como monitorar o status e os resultados do indexador da Pesquisa Cognitiva do Azure

O Azure Cognitive Search fornece informações de status e monitoramento sobre as corridas atuais e históricas de cada indexador.

O monitoramento do indexador é útil quando você deseja:

* Acompanhe o progresso de um indexador durante uma corrida contínua.
* Revise os resultados da execução de indexador em andamento ou anterior.
* Identifique erros de indexador de alto nível e erros ou avisos sobre documentos individuais sendo indexados.

## <a name="get-status-and-history"></a>Obter status e histórico

Você pode acessar informações de monitoramento de indexador de várias maneiras, incluindo:

* No [portal Azure](#portal)
* Usando a [API REST](#restapi)
* Usando o [.NET SDK](#dotnetsdk)

As informações disponíveis de monitoramento do indexador incluem todas as informações a seguir (embora os formatos de dados diferem com base no método de acesso utilizado):

* Informações de status sobre o indexador em si
* Informações sobre a execução mais recente do indexador, incluindo seu status, tempos de início e fim, e erros e avisos detalhados.
* Uma lista de indexadores históricos é executada, e seus status, resultados, erros e avisos.

Indexadores que processam grandes volumes de dados podem levar muito tempo para serem executados. Por exemplo, indexadores que lidam com milhões de documentos de origem podem ser executados por 24 horas e, em seguida, reiniciar quase imediatamente. O status dos indexadores de alto volume pode sempre dizer **Em Progresso** no portal. Mesmo quando um indexador está sendo executado, há detalhes disponíveis sobre o progresso contínuo e as corridas anteriores.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitore usando o portal

Você pode ver o status atual de todos os seus indexadores na lista **Indexers** na página Visão Geral do serviço de pesquisa.

   ![Lista de indexadores](media/search-monitor-indexers/indexers-list.png "Lista de indexadores")

Quando um indexador está sendo executado, o status na lista mostra **Em andamento**, e o valor **docs bem sucedido** mostra o número de documentos processados até agora. Pode levar alguns minutos para o portal atualizar os valores de status do indexador e a contagem de documentos.

Um indexador cuja corrida mais recente foi bem sucedida mostra **Sucesso**. Uma execução de indexador pode ser bem sucedida mesmo que documentos individuais tenham erros, se o número de erros for menor do que a configuração de itens com falha do indexador **Max.**

Se a execução mais recente terminar com um erro, o status será **executado como falho**. Um status de **Reset** significa que o estado de rastreamento de alteração do indexador foi redefinido.

Clique em um indexador na lista para ver mais detalhes sobre as corridas atuais e recentes do indexador.

   ![Histórico de resumo e execução do indexador](media/search-monitor-indexers/indexer-summary.png "Histórico de resumo e execução do indexador")

O gráfico **de resumo do Indexador** exibe um gráfico do número de documentos processados em suas corridas mais recentes.

A lista **de detalhes** da execução mostra até 50 dos resultados de execução mais recentes.

Clique em um resultado de execução na lista para ver detalhes sobre essa execução. Isso inclui seus tempos de início e fim, e quaisquer erros e avisos que ocorreram.

   ![Detalhes da execução do indexador](media/search-monitor-indexers/indexer-execution.png "Detalhes da execução do indexador")

Se houver problemas específicos de documentos durante a execução, eles serão listados nos campos Erros e Avisos.

   ![Detalhes do indexador com erros](media/search-monitor-indexers/indexer-execution-error.png "Detalhes do indexador com erros")

Avisos são comuns com alguns tipos de indexadores, e nem sempre indicam um problema. Por exemplo, indexadores que usam serviços cognitivos podem relatar avisos quando arquivos de imagem ou PDF não contêm nenhum texto para processar.

Para obter mais informações sobre como investigar erros e avisos do indexador, consulte [Problemas de solução de problemas comuns de indexador no Azure Cognitive Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitore usando APIs REST

Você pode recuperar o histórico de status e execução de um indexador usando o [comando Obter status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A resposta contém o status geral do indexador, a última invocação (ou em andamento) do indexador e o histórico de invocações recentes do indexador.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

O histórico de execução contém até as 50 corridas mais recentes, que são classificadas em ordem cronológica inversa (a primeira mais recente).

Observe que existem dois valores de status diferentes. O status de nível superior é para o próprio indexador. Um status de indexador de **execução** significa que o indexador está configurado corretamente e disponível para ser executado, mas não que ele esteja em execução no momento.

Cada execução do indexador também tem seu próprio status que indica se essa execução específica está em andamento **(em execução),** ou já concluída com um **sucesso,** **transientFailure**ou **status persistentFailure.** 

Quando um indexador é redefinido para atualizar seu estado de rastreamento de alterações, uma entrada de histórico de execução separada é adicionada com um status **Reset.**

Para obter mais detalhes sobre códigos de status e dados de monitoramento do indexador, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitore usando o .NET SDK

Você pode definir o cronograma de um indexador usando o Azure Cognitive Search .NET SDK. Para isso, inclua a propriedade **agendada** ao criar ou atualizar um Indexador.

O exemplo C# a seguir escreve informações sobre o status de um indexador e os resultados de sua execução mais recente (ou em andamento) para o console.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

A saída no console será mais ou menos assim:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Observe que existem dois valores de status diferentes. O status de nível superior é o status do indexador em si. Um status de indexador de **Execução** significa que o indexador está configurado corretamente e disponível para execução, mas não que esteja sendo executado no momento.

Cada execução do indexador também tem seu próprio status para saber se essa execução específica está em andamento **(Running),** ou já foi concluída com um status **de Sucesso** ou **Erro Transitório.** 

Quando um indexador é redefinido para atualizar seu estado de rastreamento de alterações, uma entrada de histórico separada é adicionada com um status **Reset.**

Para obter mais detalhes sobre códigos de status e informações de monitoramento do indexador, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) na API REST.

Detalhes sobre erros ou avisos específicos de documentos podem `IndexerExecutionResult.Errors` `IndexerExecutionResult.Warnings`ser recuperados enumerando as listas e .

Para obter mais informações sobre as classes SDK .NET usadas para monitorar indexadores, consulte [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) e [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
