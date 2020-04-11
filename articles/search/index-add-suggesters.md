---
title: Criar um sugestor
titleSuffix: Azure Cognitive Search
description: Habilite as ações de consulta de tipo-a-dia no Azure Cognitive Search, criando sugestões e formulando solicitações que invocam termos de consulta auto-completos ou autosugeridos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: a6c4051a5b3d557f9ac2927a62492425e7636c0d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113478"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggestions-in-azure-cognitive-search"></a>Crie um sugestionista para habilitar preenchimento automático e sugestões na Pesquisa Cognitiva do Azure

Na Pesquisa Cognitiva do Azure, a funcionalidade "search-as-you-type" ou typeahead é baseada em um construtor **sugestionante** que você adiciona a um [índice de pesquisa](search-what-is-an-index.md). Um sugestionador suporta duas variantes de pesquisa como você tipo: *preenchimento automático*, que completa o termo ou frase que você está digitando, e sugestões que retornam uma pequena lista de documentos *correspondentes.*  

A captura de tela a seguir, da [amostra Criar seu primeiro aplicativo em C#,](tutorial-csharp-type-ahead-and-suggestions.md) ilustra ambas as experiências. A auto-conclusão antecipa o que o usuário pode digitar, terminando "tw" com "in" como o termo de pesquisa prospectivo. Sugestões são resultados reais de pesquisa, cada um representando um documento correspondente. Para sugestões, você pode aparecer qualquer parte de um documento que melhor descreva o resultado. Neste exemplo, as sugestões são representadas pelo campo de nome do hotel.

![Comparação visual de consultas automáticas e sugeridas](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparação visual de consultas automáticas e sugeridas")

Para implementar esses comportamentos na Pesquisa Cognitiva do Azure, há um componente de índice e consulta. 

+ No índice, adicione um sugestionista a um índice. Você pode usar o portal, [a API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)ou [o .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). O restante deste artigo está focado na criação de um sugestionista.

+ Na solicitação de consulta, ligue para uma das [APIs listadas abaixo](#how-to-use-a-suggester).

O suporte ao tipo de pesquisa é ativado por campo. Você pode implementar ambos os comportamentos de digitação à frente dentro da mesma solução de pesquisa se quiser uma experiência semelhante à indicada na captura de tela. Ambas as solicitações visam a coleta de *documentos* de índice específico e as respostas são retornadas após um usuário ter fornecido pelo menos uma seqüência de entrada de três caracteres.

## <a name="what-is-a-suggester"></a>O que é um sugestionista?

Um sugestivo é uma estrutura de dados que suporta comportamentos de pesquisa como você, armazenando prefixos para correspondência em consultas parciais. Semelhante aos termos tokenizados, os prefixos são armazenados em índices invertidos, um para cada campo especificado em uma coleção de campos sugestor.

Ao criar prefixos, um sugestionista tem sua própria cadeia de análise, semelhante à usada para pesquisa completa de texto. No entanto, ao contrário da análise na pesquisa completa de texto, um sugestionista só pode usar analisadores predefinidos (lucene padrão, [analisadores de idiomas](index-add-language-analyzers.md)ou outros analisadores na [lista de analisadores predefinidos](index-add-custom-analyzers.md#predefined-analyzers-reference). [Analisadores](index-add-custom-analyzers.md) e configurações personalizados são especificamente proibidos para evitar configurações aleatórias que produzam resultados ruins.

> [!NOTE]
> Se você precisar contornar a restrição do analisador, use dois campos separados para o mesmo conteúdo. Isso permitirá que um dos campos tenha um sugestionador, enquanto o outro pode ser configurado com uma configuração de analisador personalizado.

## <a name="create-a-suggester"></a>Criar um sugestor

Embora um sugestionista tenha várias propriedades, é principalmente uma coleção de campos para os quais você está permitindo uma experiência de digitação antecipada. Por exemplo, um aplicativo de viagem talvez queira habilitar a pesquisa de digitação antecipada para destinos, cidades e atrações. Como tal, todos os três campos iriam para a coleção de campos.

Para criar um sugestionista, adicione um a um esquema de índice. Você pode ter um sugestionista em um índice (especificamente, um sugestionador na coleção de sugestões).

### <a name="when-to-create-a-suggester"></a>Quando criar um sugestionista

O melhor momento para criar um sugestor é quando você também está criando a própria definição de campo.

Se você tentar criar um sugestionante usando campos pré-existentes, a API irá despermitir. O texto typeahead é criado durante a indexação, quando termos parciais em duas ou mais combinações de caracteres são tokenizados ao lado de termos inteiros. Dado que os campos existentes já estão tokenizados, você terá que reconstruir o índice se quiser adicioná-los a um sugestionante. Para obter mais informações sobre reindexação, consulte [Como reconstruir um índice de pesquisa cognitiva do Azure](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Criar usando a API REST

Na API REST, adicione sugestionantes através [de Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) ou Update [Index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

### <a name="create-using-the-net-sdk"></a>Criar usando o .NET SDK

Em C#, defina um [objeto Suggester](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`é uma coleção, mas só pode levar um item. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="property-reference"></a>Referência de propriedade

|Propriedade      |Descrição      |
|--------------|-----------------|
|`name`        |O nome do sugestor.|
|`searchMode`  |A estratégia usada para pesquisar frases candidatas. O único modo com suporte atualmente é `analyzingInfixMatching`, que executa a correspondência flexível de frases no início ou no meio da frase.|
|`sourceFields`|Uma lista de um ou mais campos que são a fonte do conteúdo para obter sugestões. Os campos devem `Edm.String` `Collection(Edm.String)`ser do tipo e . Se um analisador for especificado no campo, ele deve ser um analisador nomeado [desta lista](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (não um analisador personalizado).<p/>Como uma prática recomendada, especifique apenas os campos que se prestam a uma resposta esperada e apropriada, seja uma seqüência completa em uma barra de pesquisa ou uma lista de parada.<p/>Um nome de hotel é um bom candidato porque tem precisão. Campos verboses como descrições e comentários são muito densos. Da mesma forma, campos repetitivos, como categorias e tags, são menos eficazes. Nos exemplos, incluímos "categoria" de qualquer maneira para demonstrar que você pode incluir vários campos. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Use um sugestionista em uma consulta

Depois que um sugestionador for criado, chame a API apropriada em sua lógica de consulta para invocar o recurso. 

+ [Sugestões API REST](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SugerircomhttpMessagesAsync método](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Método AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

O uso da API é ilustrado na seguinte chamada para a API Rest de preenchimento automático. Há duas tomadas deste exemplo. Primeiro, como em todas as consultas, a operação é contra a coleta de documentos de um índice. Em segundo lugar, você pode adicionar parâmetros de consulta. Embora muitos parâmetros de consulta sejam comuns a ambas as APIs, a lista é diferente para cada uma delas.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Se um sugestionador não for definido no índice, uma chamada para preenchimento automático ou sugestões falhará.

## <a name="sample-code"></a>Código de exemplo

+ [Crie seu primeiro aplicativo na](tutorial-csharp-type-ahead-and-suggestions.md) amostra C# demonstra uma construção sugestionadora, consultas sugeridas, navegação completa automaticamente e facetada. Esta amostra de código é executada em um serviço de pesquisa cognitiva azure e usa um índice de Hotéis pré-carregado, então tudo o que você precisa fazer é pressionar F5 para executar o aplicativo. Não é necessário assinar ou fazer login.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) é uma amostra mais antiga que contém código C# e Java. Ele também demonstra uma construção sugestionadora, consultas sugeridas, navegação completa e facetada. Esta amostra de código usa os dados de amostra [hospedados do NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 


## <a name="next-steps"></a>Próximas etapas

Recomendamos o seguinte exemplo para ver como as solicitações são formuladas.

> [!div class="nextstepaction"]
> [Sugestões e exemplos de autopreenchimento](search-autocomplete-tutorial.md) 
