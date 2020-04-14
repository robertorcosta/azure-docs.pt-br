---
title: Adicionar preenchimento automático e sugestões em uma caixa de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite as ações de consulta de pesquisa como você no Azure Cognitive Search, criando sugestões e formulando solicitações que preencham automaticamente uma caixa de pesquisa com termos ou frases acabados. Você também pode retornar as partidas sugeridas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 8b64a583c11e794c30e1de12eb66941874a25462
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262206"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Adicionar sugestões ou preenchimento automático ao aplicativo da Pesquisa Cognitiva do Azure

Este exemplo demonstra uma caixa de pesquisa que suporta comportamentos de pesquisa como você tipo. Existem duas características, que você pode usar em conjunto ou separadamente:

+ *Sugestões* geram resultados de pesquisa à medida que você digita, onde cada sugestão é um único resultado ou documento de pesquisa do índice que corresponde ao que você digitou até agora. 

+ *A auto-conclusão* gera consultas ao "terminar" a palavra ou a frase. Em vez de retornar os resultados, ele conclui uma consulta que você pode, em seguida, executar para retornar os resultados. Assim como acontece com as sugestões, uma palavra ou expressão concluída em uma consulta é predicada em uma correspondência no índice. O serviço não oferece consultas que não retornam resultados no índice.

O código de exemplo demonstra sugestões e preenchimento automático, tanto nas versões C# quanto javaScript. 

Desenvolvedores de C# podem percorrer um aplicativo baseado no ASP.NET MVC que usa o [SDK do .NET da Pesquisa Cognitiva do Azure](https://aka.ms/search-sdk). A lógica para fazer chamadas de consultas sugeridas e com preenchimento automático pode ser encontrada no arquivo HomeController.cs. 

Desenvolvedores de JavaScript encontrarão uma lógica de consulta equivalente em IndexJavaScript.cshtml, que inclui chamadas diretas para a [API REST da Pesquisa Cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Para as duas versões de linguagem, a experiência do usuário de front-end é baseada nas bibliotecas [jQuery UI](https://jqueryui.com/autocomplete/) e [XDSoft](https://xdsoft.net/jqplugins/autocomplete/). Usamos essas bibliotecas para criar a caixa de pesquisa com suporte para sugestões e preenchimento automático. As entradas coletadas na caixa de pesquisa são emparelhadas com ações de sugestões e preenchimento automático, como aquelas definidas em HomeController.cs ou IndexJavaScript.cshtml.

## <a name="prerequisites"></a>Pré-requisitos

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)

Um serviço de pesquisa cognitiva do Azure é opcional para este exercício porque a solução usa um serviço hospedado e um índice de demonstração nycjobs. Se você quiser construir este índice em seu próprio serviço de pesquisa, consulte [Criar o índice de empregos nyc](#configure-app) para obter instruções. Caso contrário, você pode usar o serviço e o índice existentes para apoiar um aplicativo cliente JavaScript.

<!-- The sample is comprehensive, covering suggestions, autocomplete, faceted navigation, and client-side caching. Review the readme and comments for a full description of what the sample offers. -->

## <a name="download-files"></a>Baixar arquivos

O código de exemplo para os desenvolvedores C# e JavaScript pode ser encontrado na [pasta DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) do repositório **GitHub Azure-Samples/search-dotnet-getting-getting-started** GitHub.

A amostra tem como alvo um serviço de pesquisa de demonstração existente e um índice pré-construído preenchido com [dados de demonstração do NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). O índice NYCJobs contém um [Constructo de sugestor](index-add-suggesters.md), que é um requisito para usar sugestões ou o preenchimento automático.

## <a name="run-the-sample"></a>Execute o exemplo

1. Abra **AutocompleteTutorial.sln** no Visual Studio. A solução contém um projeto mvc ASP.NET com uma conexão com um serviço de pesquisa e índice existente.

1. Atualize os pacotes NuGet:

   1. No Solution Explorer, clique com o botão direito do mouse **DotNetHowToAutoComplete** e **selecione Gerenciar pacotes NuGet**.  
   1. Selecione a guia **Atualizações,** selecione todos os pacotes e clique em **Atualizar**. Aceite qualquer contrato de licença. Mais de um passe pode ser necessário para atualizar todos os pacotes.

1. Pressione F5 para executar o projeto e carregar a página em um navegador.

Na parte superior, você verá uma opção para selecionar C# ou JavaScript. A opção de C# chama o HomeController do navegador e usa o SDK do .NET da Pesquisa Cognitiva do Azure para recuperar os resultados. 

A opção JavaScript chama a API REST da Pesquisa Cognitiva do Azure diretamente do navegador. Essa opção normalmente tem desempenho claramente melhor, pois ela usa o controlador fora do fluxo. Você pode escolher a opção que atender às suas necessidades e preferências de linguagem. Há vários exemplos de preenchimento automático na página com algumas diretrizes para cada um. Cada exemplo tem um texto de exemplo recomendado que você pode usar para experimentar.  

![Página de inicialização de exemplo](media/search-autocomplete-tutorial/startup-page.png "Página de inicialização de exemplo no localhost")

Tente digitar algumas letras em cada caixa de pesquisa para ver o que acontece.

## <a name="query-inputs"></a>Entradas de consulta

Para as versões de C# e JavaScript, a implementação da caixa de pesquisa é exatamente a mesma. 

Abra o arquivo **Index.cshtml** na pasta \Views\Home para exibir o código:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Esse exemplo é uma caixa de texto de entrada simples com uma classe para definir o estilo, uma ID para referência pelo JavaScript e o texto de espaço reservado.  A mágica está no JavaScript inserido.

A amostra de linguagem C# usa JavaScript em Index.cshtml para aproveitar a [biblioteca de autocompletar de II jQuery](https://jqueryui.com/autocomplete/). Essa biblioteca adiciona a experiência de preenchimento automático à caixa de pesquisa fazendo chamadas assíncronas ao controlador MVC a fim de recuperar as sugestões. A versão da linguagem JavaScript está em IndexJavaScript.cshtml. Ela inclui o script abaixo para a barra de pesquisa, bem como chamadas à API REST para a Pesquisa Cognitiva do Azure.

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

A função Suggest utiliza dois parâmetros que determinam se os destaques de ocorrências são retornados ou se correspondência difusa é usada em conjunto com o termo de pesquisa de entrada. O método cria um [objeto SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), que é então passado para a API sugerir. O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

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

Uma implementação JavaScript de preenchimento automático e sugestões chama a API REST, usando um URI como fonte para especificar o índice e a operação. 

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

## <a name="create-an-nycjobs-index"></a>Crie um índice NYCJobs

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
> [Preenchimento automático de sugestões de api](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [rest API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Facets em uma API Create Index REST](https://docs.microsoft.com/rest/api/searchservice/create-index)