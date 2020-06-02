---
title: Tutorial do C# Otimizar a indexação com a API de envio por push
titleSuffix: Azure Cognitive Search
description: Saiba como indexar dados com eficiência usando a API de envio por push do Azure Cognitive Search. Este tutorial e este código de exemplo estão em C#.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 85ac56eb20eabf308d6686a047d8c5ede914fed9
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82941753"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Tutorial: Como otimizar a indexação com a API de envio por push

O Azure Cognitive Search é compatível com [duas abordagens básicas](search-what-is-data-import.md) para importar dados para um índice de pesquisa: *enviar por push* os dados para o índice de maneira programática ou apontar um [indexador do Azure Cognitive Search](search-indexer-overview.md) em uma fonte de dados compatível para efetuar o *pull* nos dados.

Este tutorial descreve como indexar dados com eficiência usando o [modelo de push](search-what-is-data-import.md#pushing-data-to-an-index) por meio de solicitações de envio em lote e usando uma estratégia de repetição de retirada exponencial. Você pode [baixar e executar o aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). Este artigo explica os principais aspectos do aplicativo e os fatores a serem considerados ao indexar dados.

Este tutorial usa o C# e o [SDK do .NET](https://aka.ms/search-sdk) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Crie um índice
> * Testa vários tamanhos de lote para determinar o tamanho mais eficiente
> * Indexar dados de maneira assíncrona
> * Usar vários threads para aumentar as velocidades de indexação
> * Usar uma estratégia de repetição de retirada exponencial para repetir itens com falha

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços e as ferramentas a seguir são necessários para este tutorial.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualquer edição. O código de exemplo e as instruções foram testados na edição gratuita da Comunidade.

+ [Crie um serviço da Pesquisa Cognitiva do Azure](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual.

<a name="get-service-info"></a>

## <a name="download-files"></a>Baixar arquivos

O código-fonte para este tutorial está na pasta [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) no repositório do GitHub [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="key-considerations"></a>Considerações-chave

Ao enviar dados por push a um índice, há várias considerações importantes que afetam as velocidades de indexação. Você pode saber mais sobre esses fatores no [artigo Indexar conjuntos de dados grandes](search-howto-large-index.md).

Seis fatores principais a serem considerados são:

+ **A camada de serviço e o número de partições/réplicas** – adicionar partições e aumentar sua camada aumentarão as velocidades de indexação.
+ **Esquema de índice** – adicionar campos e adicionar propriedades adicionais a campos (como *searchable*, *facetable* ou *filterable*) reduzem as velocidades de indexação.
+ **Tamanho do lote** – o tamanho do lote ideal varia de acordo com o esquema do índice e o conjunto de dados.
+ **Número de threads/trabalhadores** – um thread não aproveitará totalmente as velocidades de indexação
+ **Estratégia de repetição** – uma estratégia de repetição de retirada exponencial deve ser usada para otimizar a indexação.
+ **Velocidades de transferência de dados de rede** – as velocidades de transferência de dados podem ser um fator limitante. Indexe os dados de dentro de seu ambiente do Azure para aumentar as velocidades de transferência de dados.


## <a name="1---create-azure-cognitive-search-service"></a>1 – Criar o serviço do Azure Cognitive Search

Para concluir este tutorial, você precisará de um serviço do Azure Cognitive Search, que você pode [criar no portal](search-create-service-portal.md). É recomendável usar a mesma camada que você planeja usar na produção para que você possa testar e otimizar com precisão as velocidades de indexação.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obter uma URL e uma chave de API de administração para a Pesquisa Cognitiva do Azure

As chamadas à API exigem a URL do serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   ![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

## <a name="2---set-up-your-environment"></a>2 – Configurar o ambiente

1. Inicie o Visual Studio e abra **OptimizeDataIndexing.sln**.
1. No Gerenciador de Soluções, abra **appsettings.json** para fornecer informações de conexão.
1. Para `searchServiceName`, se a URL completa for "https://my-demo-service.search.windows.net", o nome do serviço a ser fornecido será "my-demo-service".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 – Explorar o código

Depois de atualizar *appsettings.json*, o programa de exemplo em **OptimizeDataIndexing.sln** deverá estar pronto para ser compilado e executado.

Esse código é derivado do [Início rápido do C#](search-get-started-dotnet.md). Você pode encontrar informações mais detalhadas sobre as noções básicas de como trabalhar com o SDK do .NET nesse artigo.

Este aplicativo de console C#/.NET simples realiza as seguintes tarefas:

+ Cria um índice baseado na estrutura de dados da classe Hotel C# (que também faz referência à classe Address).
+ Testa vários tamanhos de lote para determinar o tamanho mais eficiente
+ Indexa dados de modo assíncrono
    + Usando vários threads para aumentar as velocidades de indexação
    + Usando uma estratégia de repetição de retirada exponencial para repetir itens com falha

 Antes de executar o programa, reserve um minuto para estudar o código e as definições de índice desta amostra. O código relevante está em vários arquivos:

  + **Hotel.cs** e **Address.cs** contêm o esquema que define o índice
  + **DataGenerator.cs** contém uma classe simples para facilitar a criação de grandes quantidades de dados de Hotel
  + **ExponentialBackoff.cs** contém o código para otimizar o processo de indexação conforme descrito abaixo
  + **Program.cs** contém funções que criam e excluem o índice do Azure Cognitive Search, indexa lotes de dados e testa diferentes tamanhos de lote

### <a name="creating-the-index"></a>Como criar o índice

Este programa de exemplo usa o SDK do .NET para definir e criar um índice da Pesquisa Cognitiva do Azure. Ele aproveita a classe [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) para gerar uma estrutura de índice de uma classe de modelo de dados C#.

O modelo de dados é definido pela classe Hotel, que também contém referências à classe Address. O FieldBuilder faz uma busca detalhada em várias definições de classe para gerar uma estrutura de dados complexa para o índice. As marcas de metadados são usadas para definir os atributos de cada campo, como se ele é pesquisável ou classificável.

Os snippets a seguir do arquivo **Hotel.cs** mostram como um único campo e uma referência a outra classe de modelo de dados podem ser especificados.

```csharp
. . .
[IsSearchable, IsSortable]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

No arquivo **Program.cs**, o índice é definido com um nome e uma coleção de campos gerada pelo método `FieldBuilder.BuildForType<Hotel>()` e, em seguida, é criado da seguinte maneira:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="generating-data"></a>Como gerar dados

Uma classe simples é implementada no arquivo **DataGenerator.cs** para gerar dados para teste. A única finalidade dessa classe é facilitar a geração de um grande número de documentos com uma ID exclusiva para indexação.

Para obter uma lista de 100 mil hotéis com IDs exclusivas, você executaria as duas seguintes linhas de código:

```csharp
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(100000, "large");
```

Há dois tamanhos de hotéis disponíveis para teste neste exemplo: **pequeno** e **grandes**.

O esquema do índice pode ter um impacto significativo nas velocidades de indexação. Devido a esse impacto, faz sentido converter essa classe para gerar dados que correspondem ao esquema de índice pretendido após a execução deste tutorial.

## <a name="4---test-batch-sizes"></a>4 – Tamanhos de lote de teste

O Azure Cognitive Search dá suporte às seguintes APIs para carregar um ou vários documentos em um índice:

+ [Adicionar, atualizar ou excluir documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) ou [Classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet)

A indexação de documentos em lotes aprimorará significativamente o desempenho da indexação. Esses lotes podem ter até mil documentos ou até aproximadamente 16 MB por lote.

Determinar o tamanho de lote ideal para seus dados é um componente-chave de otimização de velocidades de indexação. Os dois fatores principais que influenciam o tamanho de lote ideal são:

+ O esquema do índice
+ O tamanho dos seus dados

Como o tamanho de lote ideal depende do índice e dos dados, a melhor abordagem é testar diferentes tamanhos de lote para determinar o que resulta nas velocidades de indexação mais rápidas para seu cenário.

A função a seguir demonstra uma abordagem simples para testar tamanhos de lote.

```csharp
public static async Task TestBatchSizes(ISearchIndexClient indexClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocuments(indexClient, hotels);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }
}
```

Como nem todos os documentos têm o mesmo tamanho (embora estejam neste exemplo), estimamos o tamanho dos dados que estamos enviando para o serviço de pesquisa. Fazemos isso usando a função abaixo que primeiro converte o objeto em JSON e, em seguida, determina seu tamanho em bytes. Essa técnica nos permite determinar quais tamanhos de lote são mais eficientes em termos de velocidades de indexação de MB/s.

```csharp
public static double EstimateObjectSize(object data)
{
    // converting data to json for more accurate sizing
    var json = JsonConvert.SerializeObject(data);

    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

A função requer um `ISearchIndexClient`, bem como o número de tentativas que você gostaria de testar para cada tamanho de lote. Como pode haver uma variabilidade nas horas de indexação para cada lote, tentamos cada lote três vezes, por padrão, para tornar os resultados mais estatisticamente significativos.

```csharp
await TestBatchSizes(indexClient, numTries: 3);
```

Ao executar a função, você deverá ver uma saída como abaixo no seu console:

   ![Saída da função de tamanho do lote de teste](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Saída da função de tamanho do lote de teste")

Identifique qual tamanho de lote é mais eficiente e, em seguida, use esse tamanho de lote na próxima etapa do tutorial. Você pode ver um limite em MB/s em diferentes tamanhos de lote.

## <a name="5---index-data"></a>5 – Dados de índice

Agora que identificamos o tamanho do lote que pretendemos usar, a próxima etapa é começar a indexar os dados. Para indexar dados com eficiência, este exemplo:

* Usa vários threads/trabalhadores.
* Implementa uma estratégia de repetição de retirada exponencial.

### <a name="use-multiple-threadsworkers"></a>Usa vários threads/trabalhadores

Para aproveitar ao máximo as velocidades de indexação do Azure Cognitive Search, você provavelmente precisará usar vários threads para enviar solicitações de indexação do lote simultaneamente para o serviço.  

Várias das principais considerações mencionadas acima afetam o número ideal de threads. Você pode modificar este exemplo e testar com contagens de threads diferentes para determinar a contagem de threads ideal para seu cenário. No entanto, desde que haja vários threads em execução simultânea, você poderá aproveitar a maioria dos ganhos de eficiência.

Conforme você aumenta as solicitações que chegam ao serviço de pesquisa, pode encontrar [códigos de status HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) indicando que a solicitação não foi totalmente concluída com sucesso. Durante a indexação, dois códigos de status HTTP comuns são:

+ **503 Serviço Não Disponível** – Esse erro significa que o sistema está sob carga pesada e sua solicitação não pode ser processada no momento.
+ **207 Status Múltiplo** – esse erro significa que alguns documentos foram bem-sucedidos, mas pelo menos um deles falhou.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Implementa uma estratégia de repetição de retirada exponencial

Se ocorrer uma falha, as solicitações deverão ser repetidas usando uma [estratégia de repetição de retirada exponencial](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

O SDK do .NET do Azure Cognitive Search tenta automaticamente 503s e outras solicitações com falha, mas você precisará implementar sua lógica para tentar novamente o 207s. Ferramentas de software livre, como [Polly](https://github.com/App-vNext/Polly), também podem ser usadas para implementar uma estratégia de repetição. 

Neste exemplo, implementamos nossa própria estratégia de repetição de retirada exponencial. Para implementar essa estratégia, começamos definindo algumas variáveis, incluindo o `maxRetryAttempts` e o `delay` inicial para uma solicitação com falha:

```csharp
// Create batch of documents for indexing
IndexBatch<Hotel> batch = IndexBatch.Upload(hotels);

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

É importante capturar [IndexBatchException](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception?view=azure-dotnet), pois essas exceções indicam que a operação de indexação foi apenas parcialmente bem-sucedida (207s). Os itens com falha devem ser repetidos usando o método `FindFailedActionsToRetry` que facilita a criação de um lote contendo apenas os itens com falha.

Exceções diferentes de `IndexBatchException` também devem ser detectadas e indicar que a solicitação falhou completamente. Essas exceções são menos comuns, especialmente com o SDK do .NET, pois elas tentam 503s automaticamente.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        var response = await indexClient.Documents.IndexAsync(batch);
        break;
    }
    catch (IndexBatchException ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2}", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        // Find the failed items and create a new batch to retry
        batch = ex.FindFailedActionsToRetry(batch, x => x.HotelId);
        Console.WriteLine("Retrying failed documents using exponential backoff...\n");

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
    catch (Exception ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2} \n", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

Daqui em diante, encapsulamos o código de retirada exponencial em uma função para que ele possa ser chamado facilmente.

Outra função é então criada para gerenciar os threads ativos. Para simplificar, essa função não está incluída aqui, mas pode ser encontrada em [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/OptimizeDataIndexing/ExponentialBackoff.cs). A função pode ser chamada com o seguinte comando quando `hotels` é o dado que queremos carregar, `1000` é o tamanho do lote e `8` é o número de threads concomitantes:

```csharp
ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8).Wait();
```

Ao executar a função, você deverá ver uma saída como abaixo:

![Saída da função de dados de índice](media/tutorial-optimize-data-indexing/index-data-start.png "Saída da função de dados de índice")

Quando um lote de documentos falha, um erro é impresso indicando a falha e o lote está sendo repetido:

![Erro da função de dados de índice](media/tutorial-optimize-data-indexing/index-data-error.png "Saída da função de tamanho do lote de teste")

Depois que a função for concluída, você poderá verificar se todos os documentos foram adicionados ao índice.

## <a name="6---explore-index"></a>6 – Explorar o índice

Explore o índice de pesquisa preenchido após a execução programática do programa ou usando o [**Gerenciador de pesquisa**](search-explorer.md) no portal.

### <a name="programatically"></a>Programaticamente

Há duas opções principais para verificar o número de documentos em um índice: a [API de Contagem de Documentos](https://docs.microsoft.com/rest/api/searchservice/count-documents) e a [API Obter Estatísticas de Índice](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics). Ambos os caminhos podem exigir algum tempo adicional para atualizar, portanto, não se assuste se o número de documentos retornados for menor do que o esperado inicialmente.

#### <a name="count-documents"></a>Contar documentos

A operação Contar Documentos recupera uma contagem do número de documentos em um índice de pesquisa:

```csharp
long indexDocCount = indexClient.Documents.Count();
```

#### <a name="get-index-statistics"></a>Obter estatísticas de índice

A operação Obter Estatísticas do Índice retorna uma contagem de documentos para o índice atual, além do uso do armazenamento. As estatísticas de índice levarão mais tempo do que a contagem de documentos para serem atualizadas.

```csharp
IndexGetStatisticsResult indexStats = serviceClient.Indexes.GetStatistics(configuration["SearchIndexName"]);
```

### <a name="azure-portal"></a>Portal do Azure

No portal do Azure, abra a página **Visão Geral** do serviço de pesquisa e localize o índice **optimize-indexing** na lista **Índices**.

  ![Lista de índices da Pesquisa Cognitiva do Azure](media/tutorial-optimize-data-indexing/portal-output.png "Lista de índices da Pesquisa Cognitiva do Azure")

A *Contagem de Documentos* e *Tamanho de Armazenamento* são baseados na [API Obter Estatísticas do Índice](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) e podem levar vários minutos para ser atualizada.

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Cognitive Search e permitir que o código os recompile. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

O código de exemplo deste tutorial verifica se há índices existentes e os exclui, de modo que você possa executar novamente o código.

Você também pode usar o portal para excluir índices.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com o conceito de ingestão de dados com eficiência, vamos examinar mais de perto a arquitetura de consulta Lucene e como a pesquisa de texto completo funciona no Azure Cognitive Search.

> [!div class="nextstepaction"]
> [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
