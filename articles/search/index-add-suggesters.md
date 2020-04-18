---
title: Criar um sugestor
titleSuffix: Azure Cognitive Search
description: Habilite as ações de consulta de tipo-a-dia no Azure Cognitive Search, criando sugestões e formulando solicitações que invocam termos de consulta auto-completos ou autosugeridos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1e2a837acef976b6b872c2d4002ee49d662ad594
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641335"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Crie um sugestionista para habilitar resultados de preenchimento automático e sugeridos em uma consulta

Na Pesquisa Cognitiva do Azure, o "search-as-you-type" é habilitado através de uma construção **de sugestões** adicionada a um [índice de pesquisa](search-what-is-an-index.md). Um sugestionador suporta duas experiências: *autocompletar*, que completa o termo ou frase, e sugestões que retornam uma pequena lista de documentos *correspondentes.*  

A captura de tela a seguir de [Criar seu primeiro aplicativo em C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustra ambos. Autocompletar antecipa um termo em potencial, terminando "tw" com "in". Sugestões são mini resultados de pesquisa, onde um campo como o nome do hotel representa um documento de pesquisa de hotel correspondente do índice. Para sugestões, você pode surgir qualquer campo que forneça informações descritivas.

![Comparação visual de consultas automáticas e sugeridas](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparação visual de consultas automáticas e sugeridas")

Você pode usar esses recursos separadamente ou juntos. Para implementar esses comportamentos na Pesquisa Cognitiva do Azure, há um componente de índice e consulta. 

+ No índice, adicione um sugestionista a um índice. Você pode usar o portal, [a API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)ou [o .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). O restante deste artigo está focado na criação de um sugestionista.

+ Na solicitação de consulta, ligue para uma das [APIs listadas abaixo](#how-to-use-a-suggester).

O suporte ao tipo de pesquisa é habilitado por campo para campos de strings. Você pode implementar ambos os comportamentos de digitação à frente dentro da mesma solução de pesquisa se quiser uma experiência semelhante à indicada na captura de tela. Ambas as solicitações visam a coleta de *documentos* de índice específico e as respostas são retornadas após um usuário ter fornecido pelo menos uma seqüência de entrada de três caracteres.

## <a name="what-is-a-suggester"></a>O que é um sugestionista?

Um sugestivo é uma estrutura de dados que suporta comportamentos de pesquisa como você, armazenando prefixos para correspondência em consultas parciais. Semelhante aos termos tokenizados, os prefixos são armazenados em índices invertidos, um para cada campo especificado em uma coleção de campos sugestor.

Ao criar prefixos, um sugestionista tem sua própria cadeia de análise, semelhante à usada para pesquisa completa de texto. No entanto, ao contrário da análise em pesquisa de texto completa, um sugestionista só pode operar sobre campos que usam o analisador lucene padrão (padrão) ou um [analisador de idiomas](index-add-language-analyzers.md). Campos que usam [analisadores personalizados](index-add-custom-analyzers.md) ou [analisadores predefinidos](index-add-custom-analyzers.md#predefined-analyzers-reference) (com exceção do padrão Lucene) são explicitamente proibidos para evitar resultados ruins.

> [!NOTE]
> Se você precisar contornar a restrição do analisador, use dois campos separados para o mesmo conteúdo. Isso permitirá que um dos campos tenha um sugestionador, enquanto o outro pode ser configurado com uma configuração de analisador personalizado.

## <a name="define-a-suggester"></a>Defina um sugestionista

Para criar um sugestionista, adicione um a um [esquema de índice](https://docs.microsoft.com/rest/api/searchservice/create-index) e [defina cada propriedade](#property-reference). No índice, você pode ter um sugestionista (especificamente, um sugestionista na coleção de sugestões). O melhor momento para criar um sugestor é quando você também está definindo o campo que irá usá-lo.

### <a name="choose-fields"></a>Selecionar campos

Embora um sugestionista tenha várias propriedades, é principalmente uma coleção de campos para os quais você está habilitando uma experiência de pesquisa como você tipo. Para sugestões em particular, escolha campos que melhor representem um único resultado. Nomes, títulos ou outros campos únicos que distinguem entre várias partidas funcionam melhor. Se os campos consistem em valores repetitivos, as sugestões consistem em resultados idênticos e um usuário não saberá em qual clicar.

Certifique-se de que cada campo use um analisador que realize a análise léxica durante a indexação. Você pode usar o analisador padrão`"analyzer": null`de Lucene padrão ( `"analyzer": "en.Microsoft"`) ou um [analisador de idiomas](index-add-language-analyzers.md) (por exemplo, ). 

Sua escolha de um analisador determina como os campos são tokenizados e posteriormente prefixados. Por exemplo, para uma seqüência hifenizada como "sensível ao contexto", usar um analisador de linguagem resultará nessas combinações de tokens: "contexto", "sensível", "sensível ao contexto". Se você tivesse usado o analisador de Lucene padrão, a corda hifenizada não existiria.

> [!TIP]
> Considere usar a [API Analisar texto](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para obter informações sobre como os termos são tokenizados e posteriormente prefixados. Depois de construir um índice, você pode tentar vários analisadores em uma string para visualizar os tokens que ele emite.

### <a name="when-to-create-a-suggester"></a>Quando criar um sugestionista

O melhor momento para criar um sugestor é quando você também está criando a própria definição de campo.

Se você tentar criar um sugestionante usando campos pré-existentes, a API irá despermitir. Os prefixos são gerados durante a indexação, quando termos parciais em duas ou mais combinações de caracteres são tokenizados ao lado de termos inteiros. Dado que os campos existentes já estão tokenizados, você terá que reconstruir o índice se quiser adicioná-los a um sugestionante. Para obter mais informações, consulte [Como reconstruir um índice de pesquisa cognitiva do Azure](search-howto-reindex.md).

## <a name="create-using-rest"></a>Criar usando REST

Na API REST, adicione sugestionantes através [de Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) ou Update [Index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Criar usando .NET

Em C#, defina um [objeto Suggester](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`é uma coleção, mas só pode levar um item. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>Referência de propriedade

|Propriedade      |Descrição      |
|--------------|-----------------|
|`name`        |O nome do sugestor.|
|`searchMode`  |A estratégia usada para pesquisar frases candidatas. O único modo suportado `analyzingInfixMatching`atualmente é , que atualmente corresponde ao início de um mandato.|
|`sourceFields`|Uma lista de um ou mais campos que são a fonte do conteúdo para obter sugestões. Os campos devem `Edm.String` `Collection(Edm.String)`ser do tipo e . Se um analisador for especificado no campo, ele deve ser um analisador nomeado [desta lista](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (não um analisador personalizado).<p/> Como uma prática recomendada, especifique apenas os campos que se prestam a uma resposta esperada e apropriada, seja uma seqüência completa em uma barra de pesquisa ou uma lista de parada.<p/>Um nome de hotel é um bom candidato porque tem precisão. Campos verboses como descrições e comentários são muito densos. Da mesma forma, campos repetitivos, como categorias e tags, são menos eficazes. Nos exemplos, incluímos "categoria" de qualquer maneira para demonstrar que você pode incluir vários campos. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Use um sugestionista

Um sugestionador é usado em uma consulta. Depois que um sugestionador for criado, ligue para uma das seguintes APIs para obter uma experiência de pesquisa como você tipo:

+ [Sugestões API REST](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SugerircomhttpMessagesAsync método](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Método AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Em um aplicativo de pesquisa, o código do cliente deve aproveitar uma biblioteca como [jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) para coletar a consulta parcial e fornecer a correspondência. Para obter mais informações sobre essa tarefa, consulte [Adicionar resultados de preenchimento automático ou sugeridos ao código do cliente](search-autocomplete-tutorial.md).

O uso da API é ilustrado na seguinte chamada para a API Rest de preenchimento automático. Há duas tomadas deste exemplo. Primeiro, como em todas as consultas, a operação é contra a coleta de documentos de um índice e a consulta inclui um parâmetro **de pesquisa,** que neste caso fornece a consulta parcial. Em segundo lugar, você deve adicionar **o suggesterName** à solicitação. Se um sugestionador não for definido no índice, uma chamada para preenchimento automático ou sugestões falhará.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Código de exemplo

+ [Crie seu primeiro aplicativo em C# (lição 3 - Adicionar pesquisa como você tipo)](tutorial-csharp-type-ahead-and-suggestions.md) demonstra uma construção sugestionadora, consultas sugeridas, navegação completa automaticamente e facetada. Esta amostra de código é executada em um serviço de pesquisa cognitiva azure e usa um índice de Hotéis pré-carregado, então tudo o que você precisa fazer é pressionar F5 para executar o aplicativo. Não é necessário assinar ou fazer login.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) é uma amostra mais antiga que contém código C# e Java. Ele também demonstra uma construção sugestionadora, consultas sugeridas, navegação completa e facetada. Esta amostra de código usa os dados de amostra [hospedados do NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 

## <a name="next-steps"></a>Próximas etapas

Recomendamos o seguinte exemplo para ver como as solicitações são formuladas.

> [!div class="nextstepaction"]
> [Adicionar preenchimento automático e sugestões ao código do cliente](search-autocomplete-tutorial.md) 
