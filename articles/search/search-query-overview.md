---
title: Tipos de consultas
titleSuffix: Azure Cognitive Search
description: Saiba mais sobre os tipos de consultas com suporte no Pesquisa Cognitiva, incluindo texto livre, filtro, preenchimento automático e sugestões, pesquisa geográfica, consultas de sistema e pesquisa de documentos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007850"
---
# <a name="query-types-in-azure-cognitive-search"></a>Tipos de consulta no Azure Pesquisa Cognitiva

No Azure Pesquisa Cognitiva, uma consulta é uma especificação completa de uma operação de viagem de ida e volta, com parâmetros que regem a execução da consulta e parâmetros que modelam a resposta.

## <a name="elements-of-a-request"></a>Elementos de uma solicitação

O exemplo a seguir é uma consulta representativa criada usando a [API REST de documentos de pesquisa](/rest/api/searchservice/search-documents). Este exemplo tem como alvo o [índice de demonstração de hotéis](search-get-started-portal.md) e inclui parâmetros comuns para que você possa ter uma ideia da aparência de uma consulta.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
{
    "queryType": "simple" 
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

As consultas são sempre direcionadas à coleção de documentos de um único índice. Não é possível unir índices ou criar estruturas de dados temporárias ou personalizadas como um destino de consulta.

+ **`queryType`** define o analisador, que é o [analisador de consulta simples padrão](search-query-simple-examples.md) (ideal para pesquisa de texto completo) ou o [analisador de consulta Lucene completo](search-query-lucene-examples.md) usado para constructos de consulta avançada, como expressões regulares, pesquisa de proximidade, pesquisa difusa e de curinga, para citar alguns.

+ **`search`** fornece os critérios de correspondência, geralmente os termos ou frases inteiros, mas geralmente acompanhados por operadores boolianos. Termos de autônomo único são *termo* consultas. As consultas de várias partes entre aspas são consultas de *frases* . A pesquisa pode ser indefinida, como em **`search=*`** , mas sem critérios para correspondência, o conjunto de resultados é composto de documentos selecionados arbitrariamente.

+ **`searchFields`** restringe a execução da consulta a campos específicos. Qualquer campo atribuído como *pesquisável* no esquema de índice é um candidato para esse parâmetro.

As respostas também são moldadas pelos parâmetros que você inclui na consulta:

+ **`select`** Especifica quais campos retornar na resposta. Somente os campos marcados como *recuperáveis* no índice podem ser usados em uma instrução SELECT.

+ **`top`** Retorna o número especificado de documentos de melhor correspondência. Neste exemplo, apenas 10 ocorrências são retornadas. Você pode usar top e Skip (não mostrado) para paginar os resultados.

+ **`count`** informa quantos documentos em todo o índice correspondem em geral, o que pode ser maior que o retornado. 

+ **`orderby`** será usado se você quiser classificar os resultados por um valor, como uma classificação ou um local. Caso contrário, o padrão é usar a pontuação de relevância para classificar os resultados.

> [!Tip]
> Antes de escrever qualquer código, você pode usar ferramentas de consulta para aprender a sintaxe e experimentar com parâmetros diferentes. A abordagem mais rápida é a ferramenta interna do portal, o [Gerenciador de pesquisa](search-explorer.md).
>
> Se você seguiu este guia [de início rápido para criar o índice de demonstração de hotéis](search-get-started-portal.md), você pode colar essa cadeia de caracteres de consulta na barra de pesquisa do Explorer para executar sua primeira consulta: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>Como os atributos de campo em um índice determinam comportamentos de consulta

Design de índice e design de consulta são rigidamente acoplados ao Pesquisa Cognitiva do Azure. Um fato essencial a saber de antemão é que o *esquema de índice*, com atributos em cada campo, determina o tipo de consulta que você pode construir. 

Atributos de índice em um campo para definir as operações permitidas - se um campo está *pesquisável* no índice, *recuperáveis* nos resultados *classificável*, *filtrável* e assim por diante. Na cadeia de caracteres de consulta de exemplo, `"$orderby": "Rating"` só funciona porque o campo de classificação está marcado como *classificável* no esquema de índice. 

![Definição de índice para o exemplo de Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para o exemplo de Hotel")

A captura de tela acima é uma lista parcial de atributos de índice para o exemplo de hotéis. Você pode visualizar todo o esquema do índice no portal. Para obter mais informações sobre atributos de índice, consulte [Create Index REST API](/rest/api/searchservice/create-index).

> [!Note]
> Algumas funcionalidades de consulta está habilitada, todo o índice em vez de em uma base por campo. Esses recursos incluem: [mapas de sinônimos](search-synonyms.md), [analisadores personalizados](index-add-custom-analyzers.md), [construções de sugestores (para consultas de preenchimento automático e sugerido)](index-add-suggesters.md), [lógica de Pontuação para resultados de classificação](index-add-scoring-profiles.md).

## <a name="choose-a-parser-simple--full"></a>Escolha um analisador: simples | completo

O Azure Pesquisa Cognitiva oferece a você uma opção entre dois analisadores de consulta para lidar com consultas típicas e especializadas. Solicitações usando o analisador simples são formuladas usando a [sintaxe de consulta simples](query-simple-syntax.md), selecionada como padrão por sua velocidade e eficácia em consultas de texto de forma livre. Essa sintaxe dá suporte a vários operadores de pesquisa comuns, incluindo os operadores de precedência, E, OU, NÃO, frase e sufixo.

A [sintaxe de consulta completa do Lucene](query-Lucene-syntax.md#bkmk_syntax), ativada quando você adiciona `queryType=full` à solicitação, expõe a amplamente adotada e expressiva linguagem de consulta desenvolvida como parte do [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Sintaxe completa estende a sintaxe simple. Qualquer consulta que você escrever para a sintaxe simples será executada no analisador Lucene completo. 

Os exemplos a seguir ilustram o ponto: a mesma consulta, mas com configurações diferentes de queryType, produz resultados diferentes. Na primeira consulta, o `^3` After `historic` é tratado como parte do termo de pesquisa. O resultado mais classificado para essa consulta é "Marquis pedágio & Suites", que tem o *oceano* em sua descrição.

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

A mesma consulta que usa o analisador Lucene completo interpreta `^3` como um preforçador de termo em campo. A alternância de analisadores altera a classificação, com os resultados que contêm o termo *histórico* movendo para a parte superior.

```http
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
| [Preenchimento automático ou resultados sugeridos](search-autocomplete-tutorial.md) | Preenchimento automático ou parâmetro de sugestão | Um formulário de consulta alternativo que é executado com base em cadeias de caracteres parciais em uma experiência de pesquisa conforme o tipo. Você pode usar o preenchimento automático e as sugestões juntas ou separadamente. |
| [pesquisa difusa](query-lucene-syntax.md#bkmk_fuzzy) | Parâmetro de pesquisa e o analisador completo | Corresponde a termos com construção ou ortografia semelhantes. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [pesquisa por proximidade](query-lucene-syntax.md#bkmk_proximity) | Parâmetro de pesquisa e o analisador completo | Descobre condições que estão próximos uns dos outros em um documento. <br/>[Exemplo de pesquisa por proximidade](search-query-lucene-examples.md#example-4-proximity-search) |
| [aumento de termo](query-lucene-syntax.md#bkmk_termboost) | Parâmetro de pesquisa e o analisador completo | Classifica um documento mais alto se ele contiver o termo potencializado, em relação a outros que não contêm. <br/>[Exemplo de aumento de termo](search-query-lucene-examples.md#example-5-term-boosting) |
| [pesquisa de expressão regular](query-lucene-syntax.md#bkmk_regex) | Parâmetro de pesquisa e o analisador completo | Correspondências com base no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-6-regex) |
|  [pesquisa de curinga ou prefixo](query-lucene-syntax.md#bkmk_wildcard) | Parâmetro de pesquisa e o analisador completo | Correspondências baseadas em um prefixo e til (`~`) ou caractere único (`?`). <br/>[Exemplo de pesquisa de curinga](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Próximas etapas

Use o portal ou outra ferramenta, como o postmaster ou Visual Studio Code, ou um dos SDKs para explorar consultas com mais profundidade. Os links a seguir vão ajudá-lo a começar.

+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](./search-get-started-dotnet.md)
+ [Como consultar em REST](./search-get-started-powershell.md)