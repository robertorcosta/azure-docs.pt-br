---
title: Adicionar preenchimento automático e sugestões em uma caixa de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite as ações de consulta de pesquisa como você no Azure Cognitive Search, criando sugestões e formulando solicitações que preencham automaticamente uma caixa de pesquisa com termos ou frases acabados. Você também pode retornar as partidas sugeridas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758113"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Adicionar preenchimento automático e sugestões aos aplicativos clientes

Pesquisar como você é uma técnica comum para melhorar a produtividade de consultas iniciadas pelo usuário. No Azure Cognitive Search, essa experiência é suportada por meio de *autocompletar,* que termina um termo ou frase baseado em entrada parcial (completando "micro" com "microsoft"). Outra forma são *as sugestões*: uma pequena lista de documentos correspondentes (devolvendo títulos de livro com um ID para que você possa vincular a uma página de detalhes). Tanto a autocompletar quanto as sugestões são baseadas em uma correspondência no índice. O serviço não oferecerá consultas que retornam zero resultados.

Para implementar essas experiências na Pesquisa Cognitiva do Azure, você precisará:

+ Um *sugestionista* na parte de trás.
+ Uma *consulta* especificando [API de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ou [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) na solicitação.
+ Um *controle de iu para* lidar com interações de pesquisa como você tipo em seu aplicativo cliente. Recomendamos o uso de uma biblioteca JavaScript existente para este fim.

Na Pesquisa Cognitiva do Azure, as consultas autoconcluídas e os resultados sugeridos são recuperados do índice de pesquisa, a partir de campos selecionados que você registrou com um sugestionista. Um sugestionador faz parte do índice, e especifica quais campos fornecerão conteúdo que ou completa uma consulta, sugere um resultado ou faz ambos. Quando o índice é criado e carregado, uma estrutura de dados sugestionadora é criada internamente para armazenar prefixos usados para correspondência em consultas parciais. Para sugestões, escolher campos adequados que sejam únicos, ou pelo menos não repetitivos, é essencial para a experiência. Para obter mais informações, consulte [Criar um sugestionista](index-add-suggesters.md).

O restante deste artigo é focado em consultas e código do cliente. Ele usa JavaScript e C# para ilustrar pontos-chave. Exemplos de API REST são usados para apresentar concisamente cada operação. Para obter links para amostras de código de ponta a ponta, consulte [Próximos passos](#next-steps).

## <a name="set-up-a-request"></a>Configure um pedido

Os elementos de uma solicitação incluem uma das APIs do tipo pesquisa-como-você, uma consulta parcial e um sugestionador. O script a seguir ilustra componentes de uma solicitação, usando a API Rest autocompletar como exemplo.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

O **suggesterName** fornece os campos de reconhecimento de sugestões usados para completar termos ou sugestões. Para sugestões em particular, a lista de campo deve ser composta por aqueles que oferecem escolhas claras entre os resultados correspondentes. Em um site que vende jogos de computador, o campo pode ser o título do jogo.

O parâmetro **de pesquisa** fornece a consulta parcial, onde os caracteres são alimentados à solicitação de consulta através do controle jQuery Autocomplete. No exemplo acima, "minecraf" é uma ilustração estática do que o controle poderia ter passado.

As APIs não impõem requisitos mínimos de comprimento na consulta parcial; pode ser tão pouco quanto um personagem. No entanto, o jQuery Autocomplete fornece um comprimento mínimo. Um mínimo de dois ou três caracteres é típico.

As partidas estão no início de um termo em qualquer lugar da seqüência de entrada. Dada a "raposa marrom rápida", tanto a autocompletar como as sugestões corresponderão em versões parciais de "o", "rápido", "marrom" ou "raposa", mas não em termos parciais de infixo como "rown" ou "boi". Além disso, cada partida define o escopo para expansões a jusante. Uma consulta parcial de "br rápido" combinará em "marrom rápido" ou "pão rápido", mas nem "marrom" ou "pão" por si só seria compatível a menos que "rápido" os precedesse.

### <a name="apis-for-search-as-you-type"></a>APIs para pesquisar como você tipo

Siga estes links para as páginas de referência REST e .NET SDK:

+ [Sugestões API REST](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SugerircomhttpMessagesAsync método](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Método AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Estruturar uma resposta

Respostas para preenchimento automático e sugestões são o que você pode esperar para o padrão: [Preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) retorna uma lista de termos, [Sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) retorna termos mais um ID de documento para que você possa buscar o documento (use a API [de documento](https://docs.microsoft.com/rest/api/searchservice/lookup-document) de pesquisa para buscar o documento específico para uma página de detalhes).

As respostas são moldadas pelos parâmetros da solicitação. Para preenchimento automático, defina [**autocompleteMode**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) para determinar se a conclusão do texto ocorre em um ou dois termos. Em Sugestões, o campo escolhido determina o conteúdo da resposta.

Para sugestões, você deve refinar ainda mais a resposta para evitar duplicatas ou o que parece ser resultados não relacionados. Para controlar os resultados, inclua mais parâmetros na solicitação. Os seguintes parâmetros se aplicam tanto ao preenchimento automático quanto às sugestões, mas talvez sejam mais necessários para sugestões, especialmente quando um sugestionista inclui vários campos.

| Parâmetro | Uso |
|-----------|-------|
| **$select** | Se você tiver vários **campos de origem** em um sugestor, use **$select** para escolher qual campo contribui com valores (`$select=GameTitle`). |
| **searchFields** | Restringir a consulta a campos específicos. |
| **$filter** | Aplicar critérios de correspondência`$filter=Category eq 'ActionAdventure'`no conjunto de resultados ( ). |
| **$top** | Limitar os resultados a`$top=5`um número específico ( ).|

## <a name="add-user-interaction-code"></a>Adicionar código de interação do usuário

Preencher automaticamente um termo de consulta ou baixar uma lista de links correspondentes requer código de interação do usuário, tipicamente JavaScript, que pode consumir solicitações de fontes externas, como preenchimento automático ou consultas de sugestão contra um índice Azure Search Cognitive.

Embora você possa escrever este código nativamente, é muito mais fácil usar funções da biblioteca JavaScript existente. Este artigo demonstra dois, um para sugestões e outro para preenchimento automático. 

+ [O widget de preenchimento automático (jQuery UI)](https://jqueryui.com/autocomplete/) é usado no exemplo Sugestão. Você pode criar uma caixa de pesquisa e, em seguida, referencia-la em uma função JavaScript que usa o widget Autocomplete. Propriedades no widget definem a origem (uma função de preenchimento automático ou sugestões), o comprimento mínimo dos caracteres de entrada antes da ação ser tomada e o posicionamento.

+ [O plug-in XDSoft Autocomplete](https://xdsoft.net/jqplugins/autocomplete/) é usado como exemplo de Autocomplete.

Usamos essas bibliotecas para criar a caixa de pesquisa com suporte para sugestões e preenchimento automático. As entradas coletadas na caixa de pesquisa são emparelhadas com sugestões e ações de preenchimento automático.

## <a name="suggestions"></a>Sugestões

Esta seção orienta você através de uma implementação de resultados sugeridos, começando com a definição da caixa de pesquisa. Ele também mostra como e script que invoca a primeira biblioteca de autocompletar JavaScript referenciada neste artigo.

### <a name="create-a-search-box"></a>Criar uma caixa de pesquisa

Assumindo a [biblioteca de autocompletar a ui jQuery](https://jqueryui.com/autocomplete/) e um projeto MVC em C#, você pode definir a caixa de pesquisa usando JavaScript no arquivo **Index.cshtml.** A biblioteca adiciona a interação de pesquisa como você tipo à caixa de pesquisa, fazendo chamadas assíncronas para o controlador MVC para recuperar sugestões.

Em **Index.cshtml** na pasta \Views\Home, uma linha para criar uma caixa de pesquisa pode ser a seguinte:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Esse exemplo é uma caixa de texto de entrada simples com uma classe para definir o estilo, uma ID para referência pelo JavaScript e o texto de espaço reservado.  

Dentro do mesmo arquivo, incorpore JavaScript que faz referência à caixa de pesquisa. A função a seguir chama a API de sugerir, que solicita documentos correspondentes sugeridos com base em entradas parciais de prazo:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

O `source` programa informa a função jQuery UI Autocomplete onde obter a lista de itens a serem exibidos na caixa de pesquisa. Como este projeto é um projeto De MVC, ele chama a função **Sugerir** em **HomeController.cs** que contém a lógica para o retorno de sugestões de consulta. Essa função também passa alguns parâmetros para controlar destaques, correspondências difusas e termos. A API JavaScript de preenchimento automático adiciona o parâmetro de termo.

O `minLength: 3` garante que as recomendações só serão mostradas quando houver pelo menos três caracteres na caixa de pesquisa.

### <a name="enable-fuzzy-matching"></a>Habilite a correspondência difusa

A pesquisa difusa permite que você obtenha resultados com base em correspondências próximas, mesmo se o usuário errou a escrita de uma palavra na caixa de pesquisa. A distância de edição é 1, o que significa que pode haver uma discrepância máxima de um caractere entre a entrada do usuário e uma correspondência. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Habilite o destaque

O destaque aplica o estilo de fonte aos caracteres no resultado correspondente à entrada. Por exemplo, se a entrada parcial for "micro", o resultado apareceria como **micro**macio, **micro**escopo, e assim por diante. O destaque é baseado nos parâmetros HighlightPreTag e HighlightPostTag, definidos em linha com a função Sugestão.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Sugerir função

Se você estiver usando C# e um aplicativo MVC, **HomeController.cs** arquivo sob o diretório Controladores é onde você pode criar uma classe para resultados sugeridos. Em .NET, uma função Suggest é baseada no [método DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

O `InitSearch` método cria um cliente de índice HTTP autenticado para o serviço de pesquisa cognitiva do Azure. Para obter mais informações sobre o .NET SDK, consulte [Como usar a Pesquisa Cognitiva do Azure a partir de um aplicativo .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

A função Suggest utiliza dois parâmetros que determinam se os destaques de ocorrências são retornados ou se correspondência difusa é usada em conjunto com o termo de pesquisa de entrada. O método cria um [objeto SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), que é então passado para a API sugerir. O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

## <a name="autocomplete"></a>Preenchimento Automático

Até agora, o código UX de pesquisa foi centrado em sugestões. O próximo bloco de código mostra autocompletar, usando a função XDSoft jQuery UI Autocomplete, passando uma solicitação para autocompletar a Pesquisa Cognitiva do Azure. Como acontece com as sugestões, em um aplicativo C#, o código que suporta a interação do usuário vai para **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Função de preenchimento automático

O preenchimento automático é baseado no [método DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Como com sugestões, este bloco de código iria para o **arquivo HomeController.cs.**

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

A função Autocomplete usa a entrada do termo de pesquisa. O método cria um [objeto AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

## <a name="next-steps"></a>Próximas etapas

Siga esses links para instruções ou códigos de ponta a ponta que demonstrem experiências de pesquisa como tipo. Ambos os exemplos de código incluem implementações híbridas de sugestões e preenchimento automático juntos.

+ [Tutorial: Crie seu primeiro aplicativo em C# (lição 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C# amostra de código: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# e JavaScript com amostra de código LADO a lado REST](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)