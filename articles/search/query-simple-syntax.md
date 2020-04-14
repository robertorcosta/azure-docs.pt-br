---
title: Sintaxe de consulta simples
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta simples usada para consultas completas de pesquisa de texto na Pesquisa Cognitiva do Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258857"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples na Pesquisa Cognitiva do Azure

A Azure Cognitive Search implementa duas linguagens de consulta baseadas em Lucene: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). 

Na Pesquisa Cognitiva do Azure, a sintaxe de consulta simples exclui operações de pesquisa difusas. Em vez disso, use a sintaxe completa de Lucene para [pesquisa difusa](search-query-fuzzy.md).

> [!NOTE]
> A sintaxe de consulta simples é usada para expressões de consulta passadas no parâmetro de **pesquisa** da API documentos de [pesquisa,](https://docs.microsoft.com/rest/api/searchservice/search-documents) para não ser confundida com a [sintaxe OData](query-odata-filter-orderby-syntax.md) usada para o [parâmetro $filter](search-filters.md) dessa API. Essas diferentes sintaxes têm suas próprias regras para construir consultas, escapar de strings, e assim por diante.
>
> O Azure Cognitive Search fornece uma [sintaxe alternativa de consulta de Lucene para](query-lucene-syntax.md) consultas mais complexas no parâmetro **de pesquisa.** Para saber mais sobre a arquitetura de análise de consultas e os benefícios de cada sintaxe, consulte [Como funciona a pesquisa completa de texto no Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="invoke-simple-parsing"></a>Invoque a análise simples

A sintaxe simples é o padrão. A invocação só será necessária se você redefinir a sintaxe de completa para simples. Para definir explicitamente a sintaxe, use o parâmetro de pesquisa `queryType`. Os valores válidos `queryType=simple` incluem ou `queryType=full`, onde `simple` está o padrão, e `full` invoca o [analisador de consulta lucene completo](query-lucene-syntax.md) para consultas mais avançadas. 

## <a name="syntax-fundamentals"></a> Conceitos básicos da sintaxe

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução da consulta. O Azure Cognitive Search corresponderá a documentos contendo qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto.

Por mais simples que isso pareça, há um aspecto da execução de consultas no Azure Cognitive Search que *pode* produzir resultados inesperados, aumentando em vez de diminuir os resultados de pesquisa à medida que mais termos e operadores são adicionados à seqüência de entrada. Se essa expansão realmente ocorre depende da inclusão de um operador NOT, combinado com uma configuração de parâmetro **searchMode** que determina como NOT é interpretado em termos de e ou comportamentos. Para obter mais informações, confira [Operador NOT](#not-operator).

### <a name="precedence-operators-grouping"></a>Operadores de precedência (agrupamento)

Você pode usar parênteses para criar subconsultas, incluindo operadores dentro da instrução entre parênteses. Por exemplo, `motel+(wifi||luxury)` irá procurar documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas tem como escopo o agrupamento para um único campo. Por exemplo, `hotelAmenities:(gym+(wifi||pool))` pesquisa o campo "comodidadesDoHotel" para "academia" e "wifi" ou "academia" e "piscina".  

### <a name="escaping-search-operators"></a>Operadores de escape de pesquisa  

Para usar qualquer um dos operadores de pesquisa como parte do texto de pesquisa,`\`fuja do caractere prefixando-o com uma única barra invertida ( ). Por exemplo, para uma `https://`pesquisa `://` curinga em , onde é `search=https\:\/\/*`parte da seqüência de consulta, você especificaria . Da mesma forma, um padrão de `\+1 \(800\) 642\-7676`número de telefone fugitivo pode ser assim.

Os caracteres especiais que requerem fuga incluem o seguinte:`- * ? \ /`  

Para simplificar as coisas para os casos mais comuns, há duas exceções a essa regra em que o escape não é necessário:  

+ O operador NOT `-` só precisará ser ignorado se for o primeiro caractere após o espaço em branco, não se ele estiver no meio de um termo. Por exemplo, o GUID a seguir `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`é válido sem o caractere de fuga: .

+ O operador de sufixo `*` só precisa ser ignorado se for o último caractere após o espaço em branco, não se ele estiver no meio de um termo. Por exemplo, `4*4=16` não requer uma barra invertida.

> [!NOTE]  
> Embora escapar mantenha os tokens juntos, a [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis) durante a indexação pode tirá-los. Por exemplo, o analisador lucene padrão irá excluir e quebrar palavras em hífens, espaço branco e outros caracteres. Se você precisar de caracteres especiais na seqüência de consultas, talvez precise de um analisador que os preserve no índice. Algumas opções incluem [analisadores de linguagem](index-add-language-analyzers.md)natural da Microsoft, que preserva palavras hifenizadas, ou um analisador personalizado para padrões mais complexos. Para obter mais informações, consulte [Termos parciais, padrões e caracteres especiais](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de caracteres reservados e não seguros em URLs

Verifique se todos os caracteres reservados e não seguros estão codificados em uma URL. Por exemplo, '#' é um caractere inseguro porque é um identificador de fragmento/âncora em uma URL. O caractere deverá ser codificado como `%23` se for usado em uma URL. '&' e '=' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva do Azure. Consulte [RFC1738: Url (Uniform Resource Locators, localizadores de recursos uniformes)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

Os caracteres não seguros são ``" ` < > # % { } | \ ^ ~ [ ]``. Os caracteres reservados são `; / ? : @ = + &`.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limites de tamanho da consulta

 Há um limite para o tamanho das consultas que você pode enviar para a Pesquisa Cognitiva do Azure. Especificamente, você pode ter, no máximo, 1024 cláusulas (expressões separadas por AND, OR e assim por diante). Há também um limite de aproximadamente 32 KB em relação ao tamanho de qualquer termo individual em uma consulta. Se seu aplicativo gerar consultas de pesquisa por meio de programação, é recomendável criá-lo de forma que ele não gere consultas de tamanho ilimitado.  

## <a name="boolean-search"></a>Busca booleana

Você pode incorporar operadores booleanos (E, OU, NÃO) em uma seqüência de consultas para construir um rico conjunto de critérios contra os quais documentos correspondentes são encontrados. 

### <a name="and-operator-"></a>Operador AND `+`

O operador AND é um sinal de adição. Por exemplo, `wifi+luxury` procura documentos que contenham `wifi` e `luxury`.

### <a name="or-operator-"></a>Operador OR `|`

O operador OR é uma barra vertical ou um caractere de pipe. Por exemplo, `wifi | luxury` procura documentos que contenham `wifi`, `luxury` ou ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operador NOT `-`

O operador NOT é um sinal de subtração. Por exemplo, `wifi –luxury` procurará documentos `wifi` que tenham o termo `luxury`e/ou não tenham .

O parâmetro **searchMode** em uma solicitação de consulta controla se um termo com o operador NOT é ANDed `+` `|` ou ORed com outros termos na consulta (assumindo que não há ou operador nos outros termos). Os valores válidos incluem `any` ou `all`.

`searchMode=any`aumenta o recall de consultas, incluindo mais `-` resultados, e por padrão será interpretado como "OU NÃO". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` ou aqueles que não contêm o termo `luxury`.

`searchMode=all`aumenta a precisão das consultas, incluindo menos resultados, e por padrão - será interpretado como "E NÃO". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` e não contêm o termo "luxo". Esse é indiscutivelmente um comportamento mais intuitivo para o operador `-`. Portanto, você deve `searchMode=all` considerar `searchMode=any` usar em vez de se você quiser otimizar pesquisas de `-` precisão em vez de recall, *e* seus usuários usam frequentemente o operador em pesquisas.

Ao decidir uma configuração **searchMode,** considere os padrões de interação do usuário para consultas em vários aplicativos. Os usuários que estão procurando informações são mais propensos a incluir um operador em uma consulta, em oposição a sites de comércio eletrônico que têm estruturas de navegação mais incorporadas.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Pesquisa por prefixo

O operador de sufixo `*`é um asterisco. Por exemplo, `lingui*` encontrará "linguini" ou "linguini", ignorando caso. 

Semelhante aos filtros, uma consulta de prefixo procura uma correspondência exata. Sendo assim, não há pontuação relevante (todos os resultados recebem uma pontuação de pesquisa de 1,0). As consultas por prefixo podem ser lentas, especialmente se o índice for grande e o prefixo for composto por um pequeno número de caracteres. 

Se você quiser executar uma consulta de sufixo, combinando na última parte da string, use uma [pesquisa curinga](query-lucene-syntax.md#bkmk_wildcard) e a sintaxe de Lucene completa.

## <a name="phrase-search-"></a>Pesquisa de frases`"`

Uma pesquisa de termo é uma consulta para um ou mais termos, onde qualquer um dos termos é considerado compatível. Uma pesquisa de frases é uma `" "`frase exata entre aspas. Por exemplo, embora `Roach Motel` (sem aspas) procuraria documentos que contenham `Roach` e/ou `Motel` em qualquer lugar e em qualquer ordem, `"Roach Motel"` (com aspas) faz a correspondência apenas com documentos que contenham essa frase inteira, junta e naquela ordem (a análise de texto ainda se aplica).

## <a name="see-also"></a>Confira também  

+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para pesquisa completa de Lucene](search-query-lucene-examples.md)
+ [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe de expressão do OData](query-odata-filter-orderby-syntax.md) 
