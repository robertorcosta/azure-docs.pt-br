---
title: Tipos de consulta e composição
titleSuffix: Azure Cognitive Search
description: Noções básicas para criar uma consulta de pesquisa no Azure Pesquisa Cognitiva, usando parâmetros para filtrar, selecionar e classificar resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 0c05db39e02a6bc2a7fa5d62b8b891626eb0d241
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675808"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Tipos de consulta e composição no Azure Pesquisa Cognitiva

No Azure Pesquisa Cognitiva, uma consulta é uma especificação completa de uma operação de ida e volta. Na solicitação, há parâmetros que fornecem instruções de execução para o mecanismo, bem como parâmetros que modelam a resposta de volta. Não especificado ( `search=*` ), sem critérios de correspondência e usando parâmetros nulos ou padrão, uma consulta é executada em todos os campos pesquisáveis como uma operação de pesquisa de texto completo, retornando um conjunto de resultados sem pontuação em ordem arbitrária.

O exemplo a seguir é uma consulta representativa construída na [API REST](/rest/api/searchservice/search-documents). Este exemplo tem como alvo o [índice de demonstração de hotéis](search-get-started-portal.md) e inclui parâmetros comuns para que você possa ter uma ideia da aparência de uma consulta.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** define o analisador, que é o [analisador de consulta simples padrão](search-query-simple-examples.md) (ideal para pesquisa de texto completo) ou o [analisador de consulta Lucene completo](search-query-lucene-examples.md) usado para constructos de consulta avançada, como expressões regulares, pesquisa de proximidade, pesquisa difusa e de curinga, para citar alguns.

+ **`search`** fornece os critérios de correspondência, geralmente os termos ou frases inteiros, mas geralmente acompanhados por operadores boolianos. Termos de autônomo único são *termo* consultas. As consultas de várias partes entre aspas são consultas de *frases* . A pesquisa pode ser indefinida, como em **`search=*`** , mas sem critérios para correspondência, o conjunto de resultados é composto de documentos selecionados arbitrariamente.

+ **`searchFields`** restringe a execução da consulta a campos específicos. Qualquer campo atribuído como *pesquisável* no esquema de índice é um candidato para esse parâmetro.

As respostas também são moldadas pelos parâmetros que você inclui na consulta:

+ **`select`** Especifica quais campos retornar na resposta. Somente os campos marcados como *recuperáveis* no índice podem ser usados em uma instrução SELECT.

+ **`top`** Retorna o número especificado de documentos de melhor correspondência. Neste exemplo, apenas 10 ocorrências são retornadas. Você pode usar top e Skip (não mostrado) para paginar os resultados.

+ **`count`** informa quantos documentos em todo o índice correspondem em geral, o que pode ser maior que o retornado. 

+ **`orderby`** será usado se você quiser classificar os resultados por um valor, como uma classificação ou um local. Caso contrário, o padrão é usar a pontuação de relevância para classificar os resultados.

No Azure Pesquisa Cognitiva, a execução da consulta é sempre em relação a um índice, autenticado usando uma chave de API fornecida na solicitação. No REST, ambas são fornecidas nos cabeçalhos de solicitação.

### <a name="how-to-run-this-query"></a>Como executar essa consulta

Antes de escrever qualquer código, você pode usar ferramentas de consulta para aprender a sintaxe e experimentar com parâmetros diferentes. A abordagem mais rápida é a ferramenta interna do portal, o [Gerenciador de pesquisa](search-explorer.md).

Se você seguiu este guia [de início rápido para criar o índice de demonstração de hotéis](search-get-started-portal.md), você pode colar essa cadeia de caracteres de consulta na barra de pesquisa do Explorer para executar sua primeira consulta: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Como as operações de consulta são ativadas pelo índice

Design de índice e design de consulta são rigidamente acoplados ao Pesquisa Cognitiva do Azure. Um fato essencial a saber de antemão é que o *esquema de índice* , com atributos em cada campo, determina o tipo de consulta que você pode construir. 

Atributos de índice em um campo para definir as operações permitidas - se um campo está *pesquisável* no índice, *recuperáveis* nos resultados *classificável* , *filtrável* e assim por diante. Na cadeia de caracteres de consulta de exemplo, `"$orderby": "Rating"` só funciona porque o campo de classificação está marcado como *classificável* no esquema de índice. 

![Definição de índice para o exemplo de Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para o exemplo de Hotel")

A captura de tela acima é uma lista parcial de atributos de índice para o exemplo de hotéis. Você pode visualizar todo o esquema do índice no portal. Para obter mais informações sobre atributos de índice, consulte [Create Index REST API](/rest/api/searchservice/create-index).

> [!Note]
> Algumas funcionalidades de consulta está habilitada, todo o índice em vez de em uma base por campo. Esses recursos incluem: [mapas de sinônimos](search-synonyms.md), [analisadores personalizados](index-add-custom-analyzers.md), [construções de sugestores (para consultas de preenchimento automático e sugerido)](index-add-suggesters.md), [lógica de Pontuação para resultados de classificação](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementos de uma solicitação de consulta

Consultas são sempre direcionadas em um único índice. Não é possível unir índices ou criar estruturas de dados temporárias ou personalizadas como um destino de consulta. 

Os elementos obrigatórios em uma solicitação de consulta incluem os componentes a seguir:

+ Coleção de pontos de extremidade de serviço e de índice, expressa como uma URL que contém componentes fixos e definidos pelo usuário: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Somente REST) é necessário porque mais de uma versão da API está disponível o tempo todo. 
+ **`api-key`** , uma consulta ou uma chave de API de administração, autentica a solicitação para seu serviço.
+ **`queryType`** , simples ou completo, que pode ser omitido se você estiver usando a sintaxe simples padrão interna.
+ **`search`** ou **`filter`** fornece os critérios de correspondência, que podem ser não especificados se você quiser executar uma pesquisa vazia. Os dois tipos de consulta são discutidos em termos do analisador simples, mas até mesmo as consultas avançadas exigem o parâmetro de pesquisa para passar expressões de consulta complexas.

Todos os outros parâmetros de pesquisa são opcionais. Para obter a lista completa de atributos, consulte [criar índice (REST)](/rest/api/searchservice/create-index). Para uma análise mais detalhada de como os parâmetros são usados durante o processamento, consulte [como funciona a pesquisa de texto completo no Azure pesquisa cognitiva](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Escolher APIs e ferramentas

A tabela a seguir lista as APIs e as abordagens baseadas em ferramentas para enviar consultas.

| Metodologia | Descrição |
|-------------|-------------|
| [Gerenciador de pesquisa (Portal)](search-explorer.md) | Fornece uma barra de pesquisa e opções para seleções de versão da API e índice. Os resultados são retornados como documentos JSON. Recomendado para exploração, teste e validação. <br/>[Saiba mais.](search-get-started-portal.md#query-index) | 
| [O postmaster ou outras ferramentas REST](search-get-started-postman.md) | As ferramentas de teste da Web são uma excelente opção para formular chamadas REST. A API REST dá suporte a todas as operações possíveis no Azure Pesquisa Cognitiva. Neste artigo, saiba como configurar um cabeçalho de solicitação HTTP e o corpo para enviar solicitações para o Azure Pesquisa Cognitiva.  |
| [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) | Cliente que pode ser usado para consultar um índice de Pesquisa Cognitiva do Azure.  <br/>[Saiba mais.](search-howto-dotnet-sdk.md)  |
| [Pesquisar documentos (API REST)](/rest/api/searchservice/search-documents) | Métodos GET ou POST em um índice, usando parâmetros de consulta para entrada adicional.  |

## <a name="choose-a-parser-simple--full"></a>Escolha um analisador: simples | completo

O Azure Pesquisa Cognitiva se encontra na parte superior do Apache Lucene e oferece a você uma opção entre dois analisadores de consulta para lidar com consultas típicas e especializadas. Solicitações usando o analisador simples são formuladas usando a [sintaxe de consulta simples](query-simple-syntax.md), selecionada como padrão por sua velocidade e eficácia em consultas de texto de forma livre. Essa sintaxe dá suporte a vários operadores de pesquisa comuns, incluindo os operadores de precedência, E, OU, NÃO, frase e sufixo.

A [sintaxe de consulta completa do Lucene](query-Lucene-syntax.md#bkmk_syntax), ativada quando você adiciona `queryType=full` à solicitação, expõe a amplamente adotada e expressiva linguagem de consulta desenvolvida como parte do [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Sintaxe completa estende a sintaxe simple. Qualquer consulta que você escrever para a sintaxe simples será executada no analisador Lucene completo. 

Os exemplos a seguir ilustram o ponto: a mesma consulta, mas com configurações diferentes de queryType, produz resultados diferentes. Na primeira consulta, o `^3` After `historic` é tratado como parte do termo de pesquisa. O resultado mais classificado para essa consulta é "Marquis pedágio & Suites", que tem o *oceano* em sua descrição

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

A mesma consulta que usa o analisador Lucene completo interpreta `^3` como um preforçador de termo em campo. A alternância de analisadores altera a classificação, com os resultados que contêm o termo *histórico* movendo para a parte superior.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

O Azure Pesquisa Cognitiva dá suporte a uma ampla variedade de tipos de consulta. 

| Tipo de consulta | Uso | Obter mais informações e exemplos |
|------------|--------|-------------------------------|
| Pesquisa de texto de forma livre | Parâmetro de pesquisa e o analisador| A pesquisa de texto completo procura um ou mais termos em todos os *campos pesquisáveis* do seu índice e funciona da maneira esperada para um mecanismo de pesquisa como o Google ou o Bing. O exemplo na introdução é a pesquisa de texto completo.<br/><br/>A pesquisa de texto completo passa por uma análise lexical usando o analisador Lucene padrão (por padrão) para reduzir todos os termos, remover palavras de parada como "a". Você pode substituir o padrão por analisadores [não-inglês](index-add-language-analyzers.md#language-analyzer-list) ou [analisadores independentes de linguagem especializada](index-add-custom-analyzers.md#AnalyzerTable) que modificam a análise léxica. Um exemplo é [palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) que trata todo o conteúdo de um campo como um único token. É útil para dados como códigos postais, IDs e alguns nomes de produtos. | 
| Pesquisa filtrados | [Expressão de filtro OData](query-odata-filter-orderby-syntax.md) e qualquer analisador | As consultas de filtro avaliam uma expressão booleana em todos os campos *filtráveis* em um índice. Ao contrário de pesquisa, uma consulta de filtro corresponde o conteúdo exato de um campo, incluindo diferenciação de maiúsculas e minúsculas em campos de cadeia de caracteres. Outra diferença é que as consultas de filtro são expressas na sintaxe OData. <br/>[Exemplo de expressão de filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Pesquisa geográfica | [Tipo de EDM. geographypoint](/rest/api/searchservice/supported-data-types) no campo expressão de filtro e qualquer analisador | As coordenadas armazenadas em um campo com um Edm.GeographyPoint são usadas para controles de pesquisa "localizar perto de mim" ou baseados em mapa. <br/>[Exemplo de pesquisa geográfica](search-query-simple-examples.md#example-5-geo-search)|
| Pesquisa de intervalo | expressão de filtro e analisador simples | No Pesquisa Cognitiva do Azure, as consultas de intervalo são criadas usando o parâmetro de filtro. <br/>[Exemplo de filtro de intervalo](search-query-simple-examples.md#example-4-range-filters) | 
| [Pesquisa em campo](query-lucene-syntax.md#bkmk_fields) | Parâmetro de pesquisa e o analisador completo | Crie uma expressão de consulta composta visando um único campo. <br/>[Exemplo de pesquisa em campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [pesquisa difusa](query-lucene-syntax.md#bkmk_fuzzy) | Parâmetro de pesquisa e o analisador completo | Corresponde a termos com construção ou ortografia semelhantes. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [pesquisa por proximidade](query-lucene-syntax.md#bkmk_proximity) | Parâmetro de pesquisa e o analisador completo | Descobre condições que estão próximos uns dos outros em um documento. <br/>[Exemplo de pesquisa por proximidade](search-query-lucene-examples.md#example-4-proximity-search) |
| [aumento de termo](query-lucene-syntax.md#bkmk_termboost) | Parâmetro de pesquisa e o analisador completo | Classifica um documento mais alto se ele contiver o termo potencializado, em relação a outros que não contêm. <br/>[Exemplo de aumento de termo](search-query-lucene-examples.md#example-5-term-boosting) |
| [pesquisa de expressão regular](query-lucene-syntax.md#bkmk_regex) | Parâmetro de pesquisa e o analisador completo | Correspondências com base no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-6-regex) |
|  [pesquisa de curinga ou prefixo](query-lucene-syntax.md#bkmk_wildcard) | Parâmetro de pesquisa e o analisador completo | Correspondências baseadas em um prefixo e til (`~`) ou caractere único (`?`). <br/>[Exemplo de pesquisa de curinga](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Gerenciar resultados da pesquisa 

Os resultados da consulta são transmitidos como documentos JSON na API REST, embora, se você usar APIs do .NET, a serialização será interna. Você pode formatar resultados definindo parâmetros na consulta, selecionando campos específicos para a resposta.

Os parâmetros na consulta podem ser usados para estruturar o conjunto de resultados das seguintes maneiras:

+ Limitar ou enviar em lote o número de documentos nos resultados (50 por padrão)
+ Selecionar campos para incluir nos resultados
+ Definir uma ordem de classificação
+ Adicionar realces de ocorrência para destacar termos correspondentes no corpo dos resultados da pesquisa

### <a name="tips-for-unexpected-results"></a>Dicas para resultados inesperados

Ocasionalmente, a substância e não a estrutura dos resultados são inesperadas. Quando os resultados da consulta não forem os esperados, você poderá tentar essas modificações de consulta para verificar se os resultados melhoram:

+ Altere **`searchMode=any`** (padrão) para **`searchMode=all`** para exigir correspondências em todos os critérios em vez de qualquer um dos critérios. Isso é especialmente verdadeiro quando os operadores boolianos estão incluídos na consulta.

+ Altere a técnica de consulta se o texto ou a análise lexical for necessária, mas o tipo de consulta impede o processamento linguístico. Na pesquisa de texto completo, a análise de texto ou lexical corrige os erros de ortografia, as formas de palavra singular e até mesmo verbos ou substantivos irregulares. Para algumas consultas como pesquisa difusa ou curinga, a análise léxica não faz parte do pipeline de análise de consulta. Para alguns cenários, expressões regulares foram usadas como um solução alternativa. 

### <a name="paging-results"></a>Resultados da paginação
O Azure Pesquisa Cognitiva facilita a implementação da paginação dos resultados da pesquisa. Usando os **`top`** parâmetros e **`skip`** , você pode emitir rapidamente solicitações de pesquisa que permitem receber o conjunto total de resultados da pesquisa em subconjuntos gerenciáveis e ordenados que habilitam facilmente boas práticas de interface do usuário de pesquisa. Ao receber esses subconjuntos menores de resultados, você também pode receber a contagem de documentos no conjunto total de resultados da pesquisa.

Você pode aprender mais sobre os resultados da pesquisa de paginação no artigo como paginar [os resultados da pesquisa no Azure pesquisa cognitiva](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordenando resultados
Ao receber resultados para uma consulta de pesquisa, você pode solicitar que o Pesquisa Cognitiva do Azure forneça os resultados ordenados por valores em um campo específico. Por padrão, o Azure Pesquisa Cognitiva ordena os resultados da pesquisa com base na classificação da Pontuação de pesquisa de cada documento, que é derivada de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se você quiser que o Azure Pesquisa Cognitiva retorne os resultados ordenados por um valor diferente da Pontuação de pesquisa, você poderá usar o **`orderby`** parâmetro de pesquisa. Você pode especificar o valor do **`orderby`** parâmetro para incluir nomes de campo e chamadas para a [**`geo.distance()` função**](query-odata-filter-orderby-syntax.md) para valores geoespaciais. Cada expressão pode ser seguida por `asc` para indicar que os resultados são solicitados em ordem crescente e **`desc`** para indicar que os resultados são solicitados em ordem decrescente. Ordem ascendente da classificação padrão.


### <a name="hit-highlighting"></a>Realce de ocorrência
No Azure Pesquisa Cognitiva, enfatizando a parte exata dos resultados da pesquisa que correspondem à consulta de pesquisa é facilitada usando **`highlight`** os **`highlightPreTag`** parâmetros, e **`highlightPostTag`** . Você pode especificar quais campos *pesquisáveis* devem ter seu texto correspondente enfatizado, bem como especificar as marcas de cadeia de caracteres exatas a serem acrescentadas ao início e ao fim do texto correspondente que o Azure pesquisa cognitiva retorna.

## <a name="see-also"></a>Veja também

+ [Como a pesquisa de texto completo funciona no Azure Pesquisa Cognitiva (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](./search-get-started-dotnet.md)
+ [Como consultar em REST](./search-get-started-powershell.md)