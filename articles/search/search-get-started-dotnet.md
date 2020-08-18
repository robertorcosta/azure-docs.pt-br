---
title: 'Início Rápido: Criar um índice de pesquisa no .NET'
titleSuffix: Azure Cognitive Search
description: Neste início rápido do C#, saiba como criar um índice, carregar dados e executar consultas usando a biblioteca de clientes do Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/05/2020
ms.openlocfilehash: a2a860a2ff96c74f9d19fe7abfd845bbae8023cd
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922261"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Início Rápido: Criar um índice de pesquisa usando a biblioteca de clientes do Azure.Search.Documents

Use a nova [biblioteca de clientes do Azure.Search.Documents (versão 11)](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet) para criar um aplicativo de console do .NET Core no C# que cria, carrega e consulta um índice de pesquisa.

[Baixe o código-fonte](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart-v11) para começar com um projeto concluído ou siga as etapas neste artigo para criar o seu.

> [!NOTE]
> Procurando uma versão anterior? Confira [Criar um índice de pesquisa usando Microsoft.Azure.Search v10](search-get-started-dotnet-v10.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha as seguintes ferramentas e serviços:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/).

+ Um serviço do Azure Cognitive Search. [Criar um serviço](search-create-service-portal.md) ou [localizar um serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). É possível usar um serviço gratuito para este início rápido. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualquer edição. O código de exemplo foi testado na edição Comunidade gratuita do Visual Studio 2019.

<a name="get-service-info"></a>

## <a name="get-a-key-and-endpoint"></a>Obter uma chave e um ponto de extremidade

As chamadas ao serviço exigem um ponto de extremidade de URL e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

2. Em **Configurações** > **Chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço, necessários se você estiver criando ou excluindo objetos. Há duas chaves primárias e secundárias intercambiáveis. Você pode usar qualquer uma delas.

   ![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Inicie o Visual Studio e crie um projeto de aplicativo de console que possa ser executado no .NET Core. 

### <a name="install-the-nuget-package"></a>Instalar o pacote NuGet

Depois que o projeto for criado, adicione a biblioteca de cliente. O [pacote Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) consiste em uma biblioteca de cliente que fornece todas as APIs usadas para trabalhar com um serviço de pesquisa no .NET.

1. Em **Ferramentas** > **Gerenciador de Pacotes NuGet**, selecione **Gerenciar Pacotes NuGet para a Solução...** . 

1. Clique em **Procurar**.

1. Pesquise `Azure.Search.Documents` e selecione a versão 11.0.0.

1. Clique em **Instalar** à direita para adicionar o assembly ao projeto e à solução.

### <a name="create-a-search-client"></a>Criar um cliente de pesquisa

1. Em **Program.cs**, altere o namespace para `AzureSearch.SDK.Quickstart.v11` e, em seguida, adicione as seguintes diretivas `using`.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Crie dois clientes: [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) cria o índice e [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) funciona com um índice existente. Ambos precisam do ponto de extremidade de serviço e de uma chave de API de administração para autenticação com direitos de criação/exclusão.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient qryclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - Criar um índice

Este guia de início rápido cria um índice de Hotéis que você carregará com os dados do hotel no qual e executará consultas. Nesta etapa, defina os campos no índice. Cada definição de campo inclui nome, tipo de dados e atributos que determinam como o campo é usado.

Neste exemplo, os métodos síncronos da biblioteca Azure.Search.Documents são usados para simplificar e facilitar a legibilidade. No entanto, para cenários de produção, você deve usar métodos assíncronos para manter seu aplicativo escalonável e responsivo. Por exemplo, você usaria [CreateIndexAsync](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync), em vez de [CreateIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Adicione uma definição de classe vazia ao seu projeto: **Hotel.cs**

1. Em **Hotel.cs**, defina a estrutura de um documento do hotel.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. Em **Program.cs**, especifique os campos e atributos. [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) e [CreateIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) são usados para criar um índice.

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

Os atributos no campo determinam como ele é usado em um aplicativo. Por exemplo, o atributo `IsFilterable` deve ser atribuído a cada campo que dá suporte a uma expressão de filtro.

Em contraste com as versões anteriores do SDK do .NET que exigiam [IsSearchable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable) em campos de cadeia de caracteres pesquisáveis, você pode usar [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield) e [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield) para simplificar as definições de campo.

Semelhante às versões anteriores, outros atributos ainda são necessários na própria definição. Por exemplo, [IsFilterable](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) e [IsFacetable](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) devem ser explicitamente atribuídos, conforme mostrado no exemplo acima. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

O Azure Cognitive Search pesquisa no conteúdo armazenado no serviço. Nesta etapa, você carregará documentos JSON que estão em conformidade com o índice do hotel que você acabou de criar.

Na Pesquisa Cognitiva do Azure, os documentos são estruturas de dados que são entradas para indexação e saídas de consultas. Conforme obtido de uma fonte de dados externa, as entradas de documento podem ser linhas em um banco de dados, blobs no Armazenamento de Blobs ou documentos JSON no disco. Neste exemplo, estamos usando um atalho e inserindo documentos JSON para cinco hotéis no próprio código. 

Ao carregar documentos, você deve usar um objeto [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1). Um IndexDocumentsBatch contém uma coleção de [Ações](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), cada uma contendo um documento e uma propriedade que instrui o Azure Cognitive Search sobre qual ação executar ([fazer upload, mesclar, excluir e mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. Em **Program.cs**, crie uma matriz de documentos e ações de índice e, em seguida, passe a matriz para `ndexDocumentsBatch`. Os documentos abaixo em conformidade com o índice hotels-quickstart-v11, conforme definido pela classe Hotel.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    qryclient.IndexDocuments(batch, idxoptions);
    ```

    Depois de inicializar o objeto [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1), você pode enviá-lo ao índice chamando [IndexDocuments](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient.indexdocuments) no objeto [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient).

1. Como esse é um aplicativo de console que executa todos os comandos sequencialmente, adicione um tempo de espera de dois segundos entre a indexação e as consultas.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    O atraso de 2 segundos compensa a indexação, que é assíncrona, de modo que todos os documentos possam ser indexados antes da execução das consultas. Normalmente, a codificação em um atraso só é necessária em demonstrações, testes e aplicativos de exemplo.

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Você poderá obter os resultados da consulta, assim que o primeiro documento for indexado, mas o teste real do índice deverá aguardar até todos os documentos serem indexados.

Esta seção adiciona duas funcionalidades: lógica da consulta e resultados. Para consultas, use o método [Search](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient.search). Esse método usa o texto de pesquisa (a cadeia de consulta), bem como outras [opções](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions).

A classe [SearchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1) representa os resultados.

1. Em **Program.cs**, crie um método WriteDocuments que imprime os resultados da pesquisa no console.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Crie um método RunQueries para executar consultas e retornar resultados. Os resultados são os objetos Hotel.

    ```csharp
    private static void RunQueries(SearchClient qryclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = qryclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

Este exemplo mostra as duas [maneiras de encontrar a correspondência de termos em uma consulta](search-query-overview.md#types-of-queries): pesquisa de texto completo e filtros:

+ A pesquisa de texto completo consulta um ou mais termos em campos pesquisáveis no índice. A primeira consulta é a pesquisa de texto completo. A pesquisa de texto completo produz pontuações de relevância usadas para classificar os resultados.

+ Um filtro é uma expressão booliana avaliada em campos [IsFilterable](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) em um índice. As consultas de filtro incluem ou excluem valores. Assim, não há nenhuma pontuação de relevância associada a uma consulta de filtro. As duas últimas consultas demonstram a pesquisa de filtro.

Você pode usar a pesquisa de texto completo e os filtros juntos ou separadamente.

As pesquisas e os filtros são executados usando o método [SearchClient.Search](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient.search). Uma consulta de pesquisa pode ser passada na cadeia de caracteres `searchText`, enquanto uma expressão de filtro pode ser passada na propriedade [Filtro](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions.filter) da classe [SearchOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions). Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText` do método de [Search](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient.search). Para pesquisar sem filtrar, deixe a propriedade `Filter` não definida ou simplesmente não passe uma instância `SearchOptions`.

## <a name="run-the-program"></a>Executar o programa

Pressione F5 para recompilar o aplicativo e executar o programa em sua totalidade. 

A saída inclui mensagens de [Console.WriteLIne](https://docs.microsoft.com/dotnet/api/system.console.writeline), com a adição de resultados e informações de consulta.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Neste início rápido do C#, você trabalhou com uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Em diferentes estágios, usamos atalhos para simplificar o código a fim de facilitar a leitura e a compreensão. Caso você esteja familiarizado com os conceitos básicos, recomendaremos o próximo artigo para uma exploração das abordagens alternativas e dos conceitos que aprofundarão seu conhecimento. 

> [!div class="nextstepaction"]
> [Como realizar o desenvolvimento no .NET](search-howto-dotnet-sdk.md)

Deseja otimizar e reduzir seus gastos com a nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar os custos com o Gerenciamento de Custos](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)