---
title: Criar uma consulta básica
titleSuffix: Azure Cognitive Search
description: Saiba como construir uma solicitação de consulta no Pesquisa Cognitiva, quais ferramentas e APIs usar para teste e código e como decisões de consulta começam com design de índice.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: ad572905d9864083466049fd602e24d9f3632ea3
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387421"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>Criar uma consulta no Azure Pesquisa Cognitiva

Saiba mais sobre as ferramentas e APIs para criar uma consulta, quais métodos são usados para criar uma consulta e como a estrutura e o conteúdo do índice podem afetar os resultados da consulta. Para obter uma introdução à aparência de uma solicitação de consulta, comece com [tipos de consulta e composições](search-query-overview.md).

## <a name="choose-tools-and-apis"></a>Escolher ferramentas e APIs

Você pode usar qualquer uma das ferramentas e APIs a seguir para criar consultas para cargas de trabalho de teste ou de produção.

| Metodologia | Descrição |
|-------------|-------------|
| Portal| O [Gerenciador de pesquisa (Portal)](search-explorer.md) é uma interface de consulta no portal do Azure que pode ser usada para executar consultas em índices no serviço de pesquisa subjacente. O portal faz chamadas à API REST em segundo plano. Você pode selecionar qualquer índice e qualquer versão de API REST com suporte, incluindo versões de visualização. Uma cadeia de caracteres de consulta pode estar em sintaxe simples e completa e pode incluir expressões de filtro, facetas, instruções SELECT e searchfield e searchmode. No portal, ao abrir um índice, você pode trabalhar com o Search Explorer ao lado da definição de JSON de índice em guias lado a lado para facilitar o acesso a atributos de campo. Você pode verificar quais campos são pesquisáveis, classificável, filtráveis e com aparência durante o teste de consultas. Recomendado para investigação, teste e validação iniciais. <br/>[Saiba mais.](search-explorer.md) |
| Ferramentas de teste na Web| O [postmaster ou o Visual Studio Code](search-get-started-rest.md) são opções fortes para formular uma solicitação de [pesquisa de documentos](/rest/api/searchservice/search-documents) em repouso. A API REST dá suporte a todas as operações programáticas no Azure Pesquisa Cognitiva e, ao usar uma ferramenta como o postmaster ou o Visual Studio Code, você pode emitir solicitações interativamente para entender como ela funciona antes de investir no código. Uma ferramenta de teste na Web é uma boa opção se você não tiver direitos de colaborador ou administrativos na portal do Azure. Contanto que você tenha uma URL de pesquisa e uma chave de API de consulta, você pode usar as ferramentas para executar consultas em um índice existente. |
| SDK do Azure | Quando você estiver pronto para escrever código, poderá usar as bibliotecas de cliente do Azure.Search.Document nos SDKs do Azure para .NET, Python, JavaScript ou Java. Cada SDK está em sua própria agenda de liberação, mas você pode criar e consultar índices em todos eles. <br/><br/>[SearchClient (.net)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em C#.  [Saiba mais.](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em Python. [Saiba mais.](search-get-started-python.md) <br/><br/> [SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em JavaScript. [Saiba mais.](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>Configurar um cliente de pesquisa

Um cliente de pesquisa é autenticado no serviço de pesquisa, envia solicitações e processa respostas. Independentemente de qual ferramenta ou API você usa, um cliente de pesquisa deve ter o seguinte:

| Propriedades | Descrição |
|------------|-------------|
| Ponto de extremidade | Um serviço de pesquisa tem URL endereçável neste formato: `https://[service-name].search.windows.net` . |
| Chave de acesso da API (administrador ou consulta) | Autentica a solicitação para o serviço de pesquisa. |
| Nome do índice | As consultas são sempre direcionadas à coleção de documentos de um único índice. Não é possível unir índices ou criar estruturas de dados temporárias ou personalizadas como um destino de consulta. |
| Versão da API | As chamadas REST requerem explicitamente o `api-version` na solicitação. Por outro lado, as bibliotecas de cliente no SDK do Azure têm controle de versão em uma versão de API REST específica. Para SDKs, o `api-version` é implícito. |

### <a name="in-the-portal"></a>No portal

O Gerenciador de pesquisa e outras ferramentas de portal têm uma conexão de cliente interna com o serviço, com índices de acesso direto e outros objetos de páginas do Portal. O acesso a ferramentas, assistentes e objetos requer a associação na função colaborador ou acima no serviço. 

### <a name="using-rest"></a>Usar o REST

Para chamadas REST, você pode usar o [postmaster ou ferramentas semelhantes](search-get-started-rest.md) como o cliente para especificar uma solicitação de [pesquisa de documentos](/rest/api/searchservice/search-documents) . Cada solicitação é autônoma, portanto, você deve fornecer o ponto de extremidade, o nome do índice e a versão da API em cada solicitação. Outras propriedades, tipo de conteúdo e chave de API são passadas no cabeçalho da solicitação. 

Você pode usar POST ou GET para consultar um índice. POST, com parâmetros especificados no corpo da solicitação, é mais fácil de trabalhar com. Se você usar POST, certifique-se de incluir `docs/search` na URL:

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>Usando SDKs do Azure

Se você estiver usando um SDK do Azure, você criará o cliente no código. Todos os SDKs fornecem clientes de pesquisa que podem persistir o estado, permitindo a reutilização da conexão. Para operações de consulta, você criará uma instância de **`SearchClient`** e atribuirá valores para as seguintes propriedades: EndPoint, Key, index. Em seguida, você pode chamar o **`Search method`** para passar a cadeia de caracteres de consulta. 

| Linguagem | Cliente | Exemplo |
|----------|--------|---------|
| C# e .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Envie sua primeira consulta de pesquisa em C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Envie sua primeira consulta de pesquisa no Python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Envie sua primeira consulta de pesquisa em Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Envie sua primeira consulta de pesquisa em JavaScript](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Escolha um analisador: simples | completo

Se sua consulta for pesquisa de texto completo, um analisador será usado para processar o conteúdo do parâmetro de pesquisa. O Azure Pesquisa Cognitiva oferece dois analisadores de consulta. O analisador simples compreende a [sintaxe de consulta simples](query-simple-syntax.md). Este analisador foi selecionado como o padrão para sua velocidade e eficácia em consultas de texto de forma livre. A sintaxe dá suporte a operadores de pesquisa comuns (e, ou, não) para pesquisas de termo e frase, e a pesquisa de prefixo ( `*` ) (como em "Sea *" para Seattle e Seaside). Uma recomendação geral é testar o analisador simples primeiro e, em seguida, passar para o analisador completo se os requisitos do aplicativo chamarem consultas mais poderosas.

A [sintaxe de consulta Lucene completa](query-Lucene-syntax.md#bkmk_syntax), habilitada quando você adiciona `queryType=full` à solicitação, é baseada no [analisador do Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

A sintaxe completa é uma extensão da sintaxe simples, com mais operadores para que você possa construir consultas avançadas, como pesquisa difusa, pesquisa de caracteres curinga, pesquisa de proximidade e expressões regulares. Os exemplos a seguir ilustram o ponto: a mesma consulta, mas com **`queryType`** configurações diferentes, que produzem resultados diferentes. Na primeira consulta simples, o `^3` After `historic` é tratado como parte do termo de pesquisa. O resultado mais classificado para essa consulta é "Marquis pedágio & Suites", que tem o *oceano* em sua descrição.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

A mesma consulta que usa o analisador Lucene completo interpreta `^3` como um preforçador de termo em campo. A alternância de analisadores altera a classificação, com os resultados que contêm o termo *histórico* movendo para a parte superior.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="choose-query-methods"></a>Escolher métodos de consulta

A pesquisa é fundamentalmente um exercício controlado pelo usuário, em que os termos ou frases são coletados de uma caixa de pesquisa ou de eventos de clique em uma página. A tabela a seguir resume os mecanismos pelos quais você pode coletar entrada do usuário, juntamente com a experiência de pesquisa esperada.

| Entrada | Experiência |
|-------|---------|
| [Método de pesquisa](/rest/api/searchservice/search-documents) | Um usuário digita termos ou frases em uma caixa de pesquisa, com ou sem operadores, e clica em Pesquisar para enviar a solicitação. A pesquisa pode ser usada com filtros na mesma solicitação, mas não com preenchimento automático ou sugestões. |
| [Método de preenchimento automático](/rest/api/searchservice/autocomplete) | Um usuário digita alguns caracteres e as consultas são iniciadas depois que cada caractere novo é digitado. A resposta é uma cadeia de caracteres completa do índice. Se a cadeia de caracteres fornecida for válida, o usuário clicará em Pesquisar para enviar essa consulta ao serviço. |
| [Método Suggestions](/rest/api/searchservice/suggestions) | Assim como acontece com o preenchimento automático, um usuário digita alguns caracteres e as consultas incrementais são geradas. A resposta é uma lista suspensa de documentos correspondentes, normalmente representada por alguns campos exclusivos ou descritivos. Se qualquer uma das seleções for válida, o usuário clica em um e o documento correspondente é retornado. |
| [Navegação facetada](/rest/api/searchservice/search-documents#query-parameters) | Uma página mostra links de navegação clicáveis ou trilhas que restringem o escopo da pesquisa. Uma estrutura de navegação facetada é composta dinamicamente com base em uma consulta inicial. Por exemplo, `search=*` para preencher uma árvore de navegação facetada composta de todas as categorias possíveis. Uma estrutura de navegação facetada é criada a partir de uma resposta de consulta, mas também é um mecanismo para expressar a próxima consulta. n referência da API REST, `facets` está documentada como um parâmetro de consulta de uma operação de pesquisa de documentos, mas pode ser usada sem o `search` parâmetro.|
| [Método do filtro](/rest/api/searchservice/search-documents#query-parameters) | Os filtros são usados com facetas para restringir os resultados. Você também pode implementar um filtro por trás da página, por exemplo, para inicializar a página com campos específicos do idioma. Na referência da API REST, `$filter` é documentado como um parâmetro de consulta de uma operação pesquisar documentos, mas pode ser usado sem o `search` parâmetro.|

## <a name="know-your-field-attributes"></a>Conhecer seus atributos de campo

Se você revisou anteriormente os [conceitos básicos de uma solicitação de consulta](search-query-overview.md), talvez se lembre de que os parâmetros na solicitação de consulta dependem de como os campos são atribuídos em um índice. Por exemplo, para ser usado em uma consulta, filtro ou ordem de classificação, um campo deve ser *pesquisável*, *filtrável* e *classificável*. Da mesma forma, somente os campos marcados como *recuperáveis* podem aparecer nos resultados. Ao começar a especificar os `search` parâmetros, `filter` e `orderby` em sua solicitação, certifique-se de verificar os atributos à medida que você for para evitar resultados inesperados.

Na captura de tela do portal Abaixo do [índice de exemplo de hotéis](search-get-started-portal.md), somente os dois últimos campos "LastRenovationDate" e "rating" podem ser usados em uma `"$orderby"` única cláusula.

![Definição de índice para o exemplo de Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para o exemplo de Hotel")

Para obter uma descrição dos atributos de campo, consulte [criar índice (API REST)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Conhecer seus tokens

Durante a indexação, o mecanismo de consulta usa um analisador para executar a análise de texto em cadeias de caracteres, maximizando o potencial para correspondência no momento da consulta. No mínimo, as cadeias de caracteres estão em letras minúsculas, mas também podem passar por lematização e parar a remoção do Word. Cadeias de caracteres maiores ou palavras compostas normalmente são divididas por espaço em branco, hifens ou traços e indexadas como tokens separados. 

O ponto a ser levado aqui é que o que você considera que o índice contém, e o que realmente está nele, pode ser diferente. Se as consultas não retornarem os resultados esperados, você poderá inspecionar os tokens criados pelo analisador por meio do [analisar texto (API REST)](/rest/api/searchservice/test-analyzer). Para obter mais informações sobre a geração de tokens e o impacto sobre as consultas, consulte [pesquisa de termo parcial e padrões com caracteres especiais](search-query-partial-matching.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma melhor compreensão de como uma solicitação de consulta é construída, experimente os guias de início rápido a seguir para ter experiência prática.

+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar em REST](search-get-started-rest.md)
+ [Como consultar em .NET](search-get-started-dotnet.md)
+ [Como consultar em Python](search-get-started-python.md)
+ [Como consultar em JavaScript](search-get-started-javascript.md)