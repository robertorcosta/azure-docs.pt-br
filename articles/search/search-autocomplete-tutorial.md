---
title: Adicionar sugestões e preenchimento automático em uma caixa de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite ações de consulta com digitação antecipada na Pesquisa Cognitiva do Azure criando sugestores e formulando solicitações que preenchem uma caixa de pesquisa com termos ou frases concluídas.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 64c4e65ca7b69c7d61c706b48591ac19be3bfcf5
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792517"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Adicionar sugestões ou preenchimento automático ao aplicativo da Pesquisa Cognitiva do Azure

Neste artigo, aprenda a usar [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) e o [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) para criar uma caixa de pesquisa poderosa com suporte a comportamentos de pesquisa durante a digitação.

+ *Sugestões* são resultados sugeridos gerados à medida que você digita, com cada sugestão sendo um único resultado do índice que corresponde ao que você digitou até agora. 

+ O *preenchimento automático* "termina" a palavra ou expressão que um usuário está digitando no momento. Em vez de retornar os resultados, ele conclui uma consulta que você pode, em seguida, executar para retornar os resultados. Assim como acontece com as sugestões, uma palavra ou expressão concluída em uma consulta é predicada em uma correspondência no índice. O serviço não oferece consultas que não retornam resultados no índice.

Você pode baixar e executar o código de exemplo em **DotNetHowToAutocomplete** para avaliar esses recursos. O código de exemplo tem como alvo um índice predefinido populado com [dados de demonstração do NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). O índice NYCJobs contém um [Constructo de sugestor](index-add-suggesters.md), que é um requisito para usar sugestões ou o preenchimento automático. Você pode usar o índice preparado hospedado em um serviço de área restrita ou [popular seu próprio índice](#configure-app) usando um carregador de dados na solução de exemplo NYCJobs. 

O exemplo **DotNetHowToAutocomplete** demonstra sugestões e o preenchimento automático, nas versões de linguagem C# e JavaScript. Desenvolvedores de C# podem percorrer um aplicativo baseado no ASP.NET MVC que usa o [SDK do .NET da Pesquisa Cognitiva do Azure](https://aka.ms/search-sdk). A lógica para fazer chamadas de consultas sugeridas e com preenchimento automático pode ser encontrada no arquivo HomeController.cs. Desenvolvedores de JavaScript encontrarão uma lógica de consulta equivalente em IndexJavaScript.cshtml, que inclui chamadas diretas para a [API REST da Pesquisa Cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Para as duas versões de linguagem, a experiência do usuário de front-end é baseada nas bibliotecas [jQuery UI](https://jqueryui.com/autocomplete/) e [XDSoft](https://xdsoft.net/jqplugins/autocomplete/). Usamos essas bibliotecas para criar a caixa de pesquisa com suporte para sugestões e preenchimento automático. As entradas coletadas na caixa de pesquisa são emparelhadas com ações de sugestões e preenchimento automático, como aquelas definidas em HomeController.cs ou IndexJavaScript.cshtml.

Este exercício apresenta as seguintes tarefas:

> [!div class="checklist"]
> * Implementar uma caixa de entrada de pesquisa em JavaScript e emitir solicitações para correspondências sugeridas ou termos com preenchimento automático
> * Em C#, definir sugestões e ações de preenchimento automático em HomeController.cs
> * Em JavaScript, chamar as APIs REST diretamente para fornecer a mesma funcionalidade

## <a name="prerequisites"></a>Pré-requisitos

Um serviço da Pesquisa Cognitiva do Azure é opcional para este exercício, porque a solução usa um serviço de área restrita em tempo real que hospeda um índice de demonstração do NYCJobs preparado. Se quiser executar este exemplo em seu próprio serviço de pesquisa, consulte [Configurar o índice NYC Jobs](#configure-app) para obter instruções.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualquer edição. O código de exemplo e as instruções foram testados na edição gratuita da Comunidade.

* Baixe o [exemplo DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

O exemplo é abrangente, contendo sugestões, preenchimento automático, faceted navigation e cache do lado do cliente. Examine o arquivo Leiame e os comentários para obter uma descrição completa do que o exemplo oferece.

## <a name="run-the-sample"></a>Execute o exemplo

1. Abra **AutocompleteTutorial.sln** no Visual Studio. A solução contém um projeto do ASP.NET MVC com uma conexão ao índice de demonstração NYC Jobs.

2. Pressione F5 para executar o projeto e carregue a página em seu navegador preferido.

Na parte superior, você verá uma opção para selecionar C# ou JavaScript. A opção de C# chama o HomeController do navegador e usa o SDK do .NET da Pesquisa Cognitiva do Azure para recuperar os resultados. 

A opção JavaScript chama a API REST da Pesquisa Cognitiva do Azure diretamente do navegador. Essa opção normalmente tem desempenho claramente melhor, pois ela usa o controlador fora do fluxo. Você pode escolher a opção que atender às suas necessidades e preferências de linguagem. Há vários exemplos de preenchimento automático na página com algumas diretrizes para cada um. Cada exemplo tem um texto de exemplo recomendado que você pode usar para experimentar.  

Tente digitar algumas letras em cada caixa de pesquisa para ver o que acontece.

## <a name="search-box"></a>Caixa de pesquisa

Para as versões de C# e JavaScript, a implementação da caixa de pesquisa é exatamente a mesma. 

Abra o arquivo **Index.cshtml** na pasta \Views\Home para exibir o código:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Esse exemplo é uma caixa de texto de entrada simples com uma classe para definir o estilo, uma ID para referência pelo JavaScript e o texto de espaço reservado.  A mágica está no JavaScript inserido.

O exemplo de linguagem C# usa JavaScript no index.cshtml para aproveitar a [biblioteca de preenchimento automático da interface do usuário do jQuery](https://jqueryui.com/autocomplete/). Essa biblioteca adiciona a experiência de preenchimento automático à caixa de pesquisa fazendo chamadas assíncronas ao controlador MVC a fim de recuperar as sugestões. A versão da linguagem JavaScript está em IndexJavaScript.cshtml. Ela inclui o script abaixo para a barra de pesquisa, bem como chamadas à API REST para a Pesquisa Cognitiva do Azure.

Vamos examinar o código JavaScript para o primeiro exemplo, que chama a função de preenchimento automático da interface do usuário do jQuery, passando uma solicitação de sugestões:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

O código acima é executado no navegador no carregamento da página para configurar o preenchimento automático da interface do usuário do jQuery para a caixa de entrada "example1a".  `minLength: 3` faz com que as recomendações só sejam exibidas quando há pelo menos três caracteres na caixa de pesquisa.  O valor de origem é importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

A linha acima informa à função de preenchimento automático da interface do usuário do jQuery onde obter a lista de itens a serem mostrados na caixa de pesquisa. Como este é um projeto do MVC, ele chama a função Suggest de HomeController.cs, que contém a lógica para retornar sugestões de consulta (há mais informações sobre Suggest na próxima seção). Essa função também passa alguns parâmetros para controlar destaques, correspondências difusas e termos. A API JavaScript de preenchimento automático adiciona o parâmetro de termo.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Estendendo o exemplo para dar suporte à correspondência difusa

A pesquisa difusa permite que você obtenha resultados com base em correspondências próximas, mesmo se o usuário errou a escrita de uma palavra na caixa de pesquisa. Embora não seja obrigatória, ela aumenta significativamente a qualidade da experiência de digitação antecipada. Vamos experimentar isso alterando a linha de origem para habilitar a correspondência difusa.

Altere a linha abaixo da seguinte forma:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

para isto:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Inicie o aplicativo pressionando F5.

Tente digitar algo como "execativo" e observe como os resultados retornam para "Executivo", mesmo que não sejam uma correspondência exata para as letras digitadas.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>Preenchimento automático de jQuery com suporte do preenchimento automático da Pesquisa Cognitiva do Azure

Até agora, o código da experiência do usuário de pesquisa estava centralizado nas sugestões. O próximo bloco de código mostra a função de preenchimento automático da interface do usuário do jQuery (linha 91 de index. cshtml), passando uma solicitação de preenchimento automático da Pesquisa Cognitiva do Azure:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
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
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>Exemplo de C#

Agora que examinamos o código JavaScript da página da Web, vamos examinar o código do controlador do lado do servidor em C# que, na verdade, recupera as correspondências sugeridas usando o SDK do .NET da Pesquisa Cognitiva do Azure.

Abra o arquivo **HomeController.cs** no diretório Controllers. 

A primeira coisa que você pode notar é um método na parte superior da classe chamada `InitSearch`. Esse método cria um cliente de índice HTTP autenticado para o serviço da Pesquisa Cognitiva do Azure. Para obter mais informações, confira [Como usar a Pesquisa Cognitiva do Azure em um Aplicativo .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Na linha 41, observe a função Suggest. Ele se baseia no [método DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
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

A função Suggest utiliza dois parâmetros que determinam se os destaques de ocorrências são retornados ou se correspondência difusa é usada em conjunto com o termo de pesquisa de entrada. O método cria um [objeto SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), que é passado para a API de Sugestão. O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

Na linha 69, observe a função Autocomplete. Ele se baseia no [método DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

A função Autocomplete usa a entrada do termo de pesquisa. O método cria um [objeto AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

(Opcional) Adicione um ponto de interrupção no início da função Suggest e explore o código. Observe a resposta retornada pelo SDK e como ela é convertida no resultado retornado pelo método.

Os outros exemplos na página seguem o mesmo padrão para adicionar o realce de ocorrências e facetas para dar suporte ao armazenamento em cache dos resultados de preenchimento automático no lado do cliente. Examine cada um deles para entender como eles funcionam e como aproveitá-los em sua experiência de pesquisa.

## <a name="javascript-example"></a>Exemplo de JavaScript

Uma implementação de JavaScript de preenchimento automático e sugestões chama a API REST, usando um URI como a origem para especificar o índice e a operação. 

Para examinar a implementação do JavaScript, abra **IndexJavaScript.cshtml**. Observe que a função de preenchimento automático da interface do usuário do jQuery também é usada para a caixa de pesquisa, coletando entradas de termo de pesquisa e fazendo chamadas assíncronas para a Pesquisa Cognitiva do Azure para recuperar correspondências sugeridas ou termos concluídos. 

Vamos examinar o código JavaScript do primeiro exemplo:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Se comparar esse exemplo com o exemplo acima que chama o controlador Home, você verá várias semelhanças.  A configuração de preenchimento automático para `minLength` e `position` são exatamente iguais. 

A alteração significativa aqui está na origem. Em vez de chamar o método Suggest no controlador principal, uma solicitação REST é criada em uma função de JavaScript e executada usando o Ajax. A resposta, em seguida, é processada em "success" e usada como a origem.

As chamadas REST usam URIs para especificar se uma chamada à API de [Preenchimento Automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ou [Sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) está sendo feita. Os URIs a seguir estão nas linhas 9 e 10, respectivamente.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Na linha 148, você pode encontrar um script que chama o `autocompleteUri`. A primeira chamada para `suggestUri` está na linha 39.

> [!Note]
> A capacidade de fazer chamadas REST para o serviço em JavaScript é oferecida aqui como uma demonstração conveniente da API REST, mas não deve ser interpretada como uma melhor prática ou recomendação. A inclusão de uma chave de API e de um ponto de extremidade em um script abre seu serviço para ataques de negação de serviço para qualquer pessoa que possa ler esses valores do script. Embora seja seguro usar JavaScript para fins de aprendizado, e talvez em índices hospedados no serviço gratuito, é recomendável usar C# ou Java para operações de indexação e consulta no código de produção. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Configurar NYCJobs para ser executado em seu serviço

Até agora, você usou o índice de demonstração NYCJobs hospedado. Se quiser ter visibilidade total do código, incluindo do índice, siga estas instruções para criar e carregar o índice em seu próprio serviço de pesquisa.

1. [Crie um serviço da Pesquisa Cognitiva do Azure](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este exemplo. 

   > [!Note]
   > Se estiver usando o serviço gratuito da Pesquisa Cognitiva do Azure, você estará limitado a três índices. O carregador de dados NYCJobs cria dois índices. Não se esqueça de deixar um espaço em seu serviço para aceitar os novos índices.

1. Baixe o código de exemplo [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

1. Na pasta DataLoader do código de exemplo NYCJobs, abra **DataLoader.sln** no Visual Studio.

1. Adicione as informações de conexão para seu serviço da Pesquisa Cognitiva do Azure. Abra o App.config dentro do projeto DataLoader e altere o appSettings TargetSearchServiceName e TargetSearchServiceApiKey para refletir seu serviço da Pesquisa Cognitiva do Azure e a chave de API do serviço da Pesquisa Cognitiva do Azure. Essas informações podem ser encontradas no portal do Azure.

1. Pressione F5 para iniciar o aplicativo, criando dois índices e importando os dados de exemplo de NYCJob.

1. Abra **AutocompleteTutorial.sln** e edite o Web.config no projeto **AutocompleteTutorial**. Altere os valores de SearchServiceName e SearchServiceApiKey para valores válidos para o serviço de pesquisa.

1. Pressione F5 para executar o aplicativo. O aplicativo Web de exemplo é aberto no navegador padrão. A experiência é idêntica à versão da área restrita, somente o índice e os dados são hospedados em seu serviço.

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstra as etapas básicas para a criação de uma caixa de pesquisa com suporte ao preenchimento automático e a sugestões. Você viu como poderia criar um aplicativo ASP.NET MVC e usar a API REST ou o SDK do .NET da Pesquisa Cognitiva do Azure para recuperar sugestões.

Como a etapa seguinte, integre sugestões e preenchimento automático à sua experiência de pesquisa. Os artigos de referência a seguir devem ajudar.

> [!div class="nextstepaction"]
> [API REST de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atributo de índice de facetas em uma API REST de criação de índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

