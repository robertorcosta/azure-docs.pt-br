---
title: Tipos de consulta e composição
titleSuffix: Azure Cognitive Search
description: Noções básicas para criar uma consulta de pesquisa no Azure Pesquisa Cognitiva, usando parâmetros para filtrar, selecionar e classificar resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793210"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Tipos de consulta e composição no Azure Pesquisa Cognitiva

No Azure Pesquisa Cognitiva, uma consulta é uma especificação completa de uma operação de ida e volta. Os parâmetros na solicitação fornecem critérios de correspondência para localizar documentos em um índice, quais campos incluir ou excluir, instruções de execução passadas para o mecanismo e diretivas para formatar a resposta. Não especificado (`search=*`), uma consulta é executada em todos os campos pesquisáveis como uma operação de pesquisa de texto completo, retornando um conjunto de resultados não pontuados em ordem arbitrária.

O exemplo a seguir é uma consulta representativa construída na [API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). Este exemplo visa o [índice de demonstração de hotéis](search-get-started-portal.md) e inclui parâmetros comuns.

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

+ **`queryType`** define o analisador, que é o [analisador de consulta simples padrão](search-query-simple-examples.md) (ideal para pesquisa de texto completo) ou o [analisador de consulta Lucene completo](search-query-lucene-examples.md) usado para construções de consulta avançada, como expressões regulares, pesquisa de proximidade, difusa e pesquisa de curingas, para citar alguns.

+ **`search`** fornece os critérios de correspondência, geralmente texto, mas geralmente acompanhados por operadores booleanos. Termos de autônomo único são *termo* consultas. Consultas de várias partes com delimitação de cotação são *consultas de frase-chave*. A pesquisa pode ser indefinida, como em **`search=*`** , mas mais provavelmente consiste em termos, expressões e operadores semelhantes ao que aparece no exemplo.

+ **`searchFields`** restringe a execução da consulta a campos específicos. Qualquer campo atribuído como *pesquisável* no esquema de índice é um candidato para esse parâmetro.

As respostas são moldadas também pelos parâmetros incluídos na consulta. No exemplo, o conjunto de resultados consiste em campos listados na instrução **`select`** . Somente os campos marcados como *recuperáveis* podem ser usados em uma instrução $SELECT. Além disso, somente os **`top`** 10 ocorrências são retornados nessa consulta, enquanto **`count`** informa quantos documentos correspondem ao geral, o que pode ser maior que o retornado. Nessa consulta, as linhas são classificadas por classificação em ordem decrescente.

No Azure Pesquisa Cognitiva, a execução da consulta é sempre em relação a um índice, autenticado usando uma chave de API fornecida na solicitação. No REST, ambas são fornecidas nos cabeçalhos de solicitação.

### <a name="how-to-run-this-query"></a>Como executar essa consulta

Para executar essa consulta, use [o Search Explorer e o índice de demonstração de hotéis](search-get-started-portal.md). 

Você pode colar essa cadeia de caracteres de consulta na barra de pesquisa do explorer: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Como as operações de consulta são ativadas pelo índice

Design de índice e design de consulta são rigidamente acoplados ao Pesquisa Cognitiva do Azure. Um fato essencial a saber de antemão é que o *esquema de índice*, com atributos em cada campo, determina o tipo de consulta que você pode construir. 

Atributos de índice em um campo para definir as operações permitidas - se um campo está *pesquisável* no índice, *recuperáveis* nos resultados *classificável*,  *filtrável*e assim por diante. Na cadeia de caracteres de consulta de exemplo, `"$orderby": "Rating"` só funciona porque o campo de classificação está marcado como *classificável* no esquema de índice. 

![Definição de índice para o exemplo de Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para o exemplo de Hotel")

A captura de tela acima é uma lista parcial de atributos de índice para o exemplo de hotéis. Você pode visualizar todo o esquema do índice no portal. Para obter mais informações sobre atributos de índice, consulte [Create Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Algumas funcionalidades de consulta está habilitada, todo o índice em vez de em uma base por campo. Esses recursos incluem: [mapas de sinônimos](search-synonyms.md), [analisadores personalizados](index-add-custom-analyzers.md), [construções de sugestores (para consultas de preenchimento automático e sugerido)](index-add-suggesters.md), [lógica de Pontuação para resultados de classificação](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementos de uma solicitação de consulta

Consultas são sempre direcionadas em um único índice. Não é possível unir índices ou criar estruturas de dados temporárias ou personalizadas como um destino de consulta. 

Os elementos obrigatórios em uma solicitação de consulta incluem os componentes a seguir:

+ Coleta de documentos de endpoint e índice de serviço, expressa como uma URL contendo componentes fixos e definidos pelo usuário: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Somente REST) é necessária porque mais de uma versão da API está disponível em todos os momentos. 
+ **`api-key`** , seja uma consulta ou uma chave de administrador, autentica a solicitação em seu serviço.
+ **`queryType`** , seja simples ou completo, que pode ser omitido se você estiver usando a sintaxe simples padrão incorporada.
+ **`search`** ou **`filter`** fornece os critérios de correspondência, que podem não ser especificados se você quiser realizar uma pesquisa vazia. Os dois tipos de consulta são discutidos em termos do analisador simples, mas até mesmo as consultas avançadas exigem o parâmetro de pesquisa para passar expressões de consulta complexas.

Todos os outros parâmetros de pesquisa são opcionais. Para obter a lista completa de atributos, consulte [criar índice (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Para uma análise mais detalhada de como os parâmetros são usados durante o processamento, consulte [como funciona a pesquisa de texto completo no Azure pesquisa cognitiva](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Escolher APIs e ferramentas

A tabela a seguir lista as APIs e as abordagens baseadas em ferramentas para enviar consultas.

| Metodologia | Descrição |
|-------------|-------------|
| [Gerenciador de pesquisa (portal)](search-explorer.md) | Fornece uma barra de pesquisa e opções para seleções de versão da API e índice. Os resultados são retornados como documentos JSON. Recomendado para exploração, teste e validação. <br/>[Saiba mais.](search-get-started-portal.md#query-index) | 
| [O postmaster ou outras ferramentas REST](search-get-started-postman.md) | As ferramentas de teste da Web são uma excelente opção para formular chamadas REST. A API REST dá suporte a todas as operações possíveis no Azure Pesquisa Cognitiva. Neste artigo, saiba como configurar um cabeçalho de solicitação HTTP e o corpo para enviar solicitações para o Azure Pesquisa Cognitiva.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Cliente que pode ser usado para consultar um índice de Pesquisa Cognitiva do Azure.  <br/>[Saiba mais.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Pesquisar documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Métodos GET ou POST em um índice, usando parâmetros de consulta para entrada adicional.  |

## <a name="choose-a-parser-simple--full"></a>Escolha um analisador: simples | completo

O Azure Pesquisa Cognitiva se encontra na parte superior do Apache Lucene e oferece a você uma opção entre dois analisadores de consulta para lidar com consultas típicas e especializadas. Solicitações usando o analisador simples são formuladas usando a [sintaxe de consulta simples](query-simple-syntax.md), selecionada como padrão por sua velocidade e eficácia em consultas de texto de forma livre. Essa sintaxe dá suporte a vários operadores de pesquisa comuns, incluindo os operadores de precedência, E, OU, NÃO, frase e sufixo.

A [sintaxe de consulta completa do Lucene](query-Lucene-syntax.md#bkmk_syntax), ativada quando você adiciona `queryType=full` à solicitação, expõe a amplamente adotada e expressiva linguagem de consulta desenvolvida como parte do [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Sintaxe completa estende a sintaxe simple. Qualquer consulta que você escrever para a sintaxe simples será executada no analisador Lucene completo. 

Os exemplos a seguir ilustram o ponto: a mesma consulta, mas com configurações diferentes de queryType, produz resultados diferentes. Na primeira consulta, o `^3` depois que `historic` é tratado como parte do termo de pesquisa. O resultado mais classificado para essa consulta é "Marquis pedágio & Suites", que tem o *oceano* em sua descrição

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

A mesma consulta que usa o analisador Lucene completo interpreta `^3` como um reforçador de termo em campo. A alternância de analisadores altera a classificação, com os resultados que contêm o termo *histórico* movendo para a parte superior.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

O Azure Pesquisa Cognitiva dá suporte a uma ampla variedade de tipos de consulta. 

| Tipo de consulta | Uso | Obter mais informações e exemplos |
|------------|--------|-------------------------------|
| Pesquisa de texto de forma livre | Parâmetro de pesquisa e o analisador| A pesquisa de texto completo procura um ou mais termos em todos os *campos pesquisáveis* do seu índice e funciona da maneira esperada para um mecanismo de pesquisa como o Google ou o Bing. O exemplo na introdução é a pesquisa de texto completo.<br/><br/>A pesquisa de texto completo é submetida à análise de texto usando o analisador padrão Lucene (por padrão) para diminuir todos os termos, remover palavras parciais como "o". Você pode substituir o padrão por [analisadores que não estão em inglês](index-add-language-analyzers.md#language-analyzer-list) ou [analisadores especializados independentes de linguagem](index-add-custom-analyzers.md#AnalyzerTable) que modificam a análise de texto. Um exemplo é [palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) que trata todo o conteúdo de um campo como um único token. É útil para dados como códigos postais, IDs e alguns nomes de produtos. | 
| Pesquisa filtrados | [Expressão de filtro OData](query-odata-filter-orderby-syntax.md) e qualquer analisador | As consultas de filtro avaliam uma expressão booleana em todos os campos *filtráveis* em um índice. Ao contrário de pesquisa, uma consulta de filtro corresponde o conteúdo exato de um campo, incluindo diferenciação de maiúsculas e minúsculas em campos de cadeia de caracteres. Outra diferença é que as consultas de filtro são expressas na sintaxe OData. <br/>[Exemplo de expressão de filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Pesquisa geográfica | [Tipo de EDM. geographypoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) no campo expressão de filtro e qualquer analisador | As coordenadas armazenadas em um campo com um Edm.GeographyPoint são usadas para controles de pesquisa "localizar perto de mim" ou baseados em mapa. <br/>[Exemplo de pesquisa geográfica](search-query-simple-examples.md#example-5-geo-search)|
| Pesquisa de intervalo | expressão de filtro e analisador simples | No Pesquisa Cognitiva do Azure, as consultas de intervalo são criadas usando o parâmetro de filtro. <br/>[Exemplo de filtro de intervalo](search-query-simple-examples.md#example-4-range-filters) | 
| [Pesquisa em campo](query-lucene-syntax.md#bkmk_fields) | Parâmetro de pesquisa e o analisador completo | Crie uma expressão de consulta composta visando um único campo. <br/>[Exemplo de pesquisa em campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [pesquisa difusa](query-lucene-syntax.md#bkmk_fuzzy) | Parâmetro de pesquisa e o analisador completo | Corresponde a termos com construção ou ortografia semelhantes. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [pesquisa por proximidade](query-lucene-syntax.md#bkmk_proximity) | Parâmetro de pesquisa e o analisador completo | Descobre condições que estão próximos uns dos outros em um documento. <br/>[Exemplo de pesquisa por proximidade](search-query-lucene-examples.md#example-4-proximity-search) |
| [aumento de termos](query-lucene-syntax.md#bkmk_termboost) | Parâmetro de pesquisa e o analisador completo | Classifica um documento mais alto se ele contiver o termo potencializado, em relação a outros que não contêm. <br/>[Exemplo de aumento de termo](search-query-lucene-examples.md#example-5-term-boosting) |
| [pesquisa com expressão regular](query-lucene-syntax.md#bkmk_regex) | Parâmetro de pesquisa e o analisador completo | Correspondências com base no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-6-regex) |
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

+ Altere **`searchMode=any`** (padrão) para **`searchMode=all`** para exigir correspondências em todos os critérios, em vez de em qualquer um dos critérios. Isso é especialmente verdadeiro quando os operadores boolianos estão incluídos na consulta.

+ Altere a técnica de consulta se o texto ou a análise lexical for necessária, mas o tipo de consulta impede o processamento linguístico. Na pesquisa de texto completo, a análise de texto ou lexical corrige os erros de ortografia, as formas de palavra singular e até mesmo verbos ou substantivos irregulares. Para algumas consultas, como pesquisa difusa ou com curinga, a análise de texto não faz parte do pipeline de análise de consulta. Para alguns cenários, expressões regulares foram usadas como um solução alternativa. 

### <a name="paging-results"></a>Resultados da paginação
O Azure Pesquisa Cognitiva facilita a implementação da paginação dos resultados da pesquisa. Ao usar os parâmetros **`top`** e **`skip`** , você pode emitir solicitações de pesquisa que permitem que você receba o conjunto total de resultados de pesquisa em subconjuntos organizáveis e ordenados que facilitam facilmente uma boa pesquisar práticas de interface do usuário. Ao receber esses subconjuntos menores de resultados, você também pode receber a contagem de documentos no conjunto total de resultados da pesquisa.

Você pode aprender mais sobre os resultados da pesquisa de paginação no artigo como paginar [os resultados da pesquisa no Azure pesquisa cognitiva](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordenando resultados
Ao receber resultados para uma consulta de pesquisa, você pode solicitar que o Pesquisa Cognitiva do Azure forneça os resultados ordenados por valores em um campo específico. Por padrão, o Azure Pesquisa Cognitiva ordena os resultados da pesquisa com base na classificação da Pontuação de pesquisa de cada documento, que é derivada de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se você quiser que o Azure Pesquisa Cognitiva retorne os resultados ordenados por um valor diferente da Pontuação de pesquisa, você poderá usar o parâmetro de pesquisa **`orderby`** . Você pode especificar o valor do parâmetro **`orderby`** para incluir nomes de campo e chamadas para a [ **`geo.distance()` função**](query-odata-filter-orderby-syntax.md) para valores geoespaciais. Each expression can be followed by `asc` to indicate that results are requested in ascending order, and **`desc`** to indicate that results are requested in descending order. Ordem ascendente da classificação padrão.


### <a name="hit-highlighting"></a>Realce de ocorrência
No Azure Pesquisa Cognitiva, enfatizando a parte exata dos resultados da pesquisa que correspondem à consulta de pesquisa é facilitada usando os parâmetros **`highlight`** , **`highlightPreTag`** e **`highlightPostTag`** . Você pode especificar quais campos *pesquisáveis* devem ter seu texto correspondente enfatizado, bem como especificar as marcas de cadeia de caracteres exatas a serem acrescentadas ao início e ao fim do texto correspondente que o Azure pesquisa cognitiva retorna.

## <a name="see-also"></a>Consulte

+ [Como a pesquisa de texto completo funciona no Azure Pesquisa Cognitiva (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](search-query-dotnet.md)
+ [Como consultar em REST](search-create-index-rest-api.md)
