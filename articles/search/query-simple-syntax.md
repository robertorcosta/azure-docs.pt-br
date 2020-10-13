---
title: Sintaxe de consulta simples
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta simples usada para consultas de pesquisa de texto completo no Azure Pesquisa Cognitiva.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923374"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva implementa duas linguagens de consulta baseadas em Lucene: [analisador de consulta simples](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o [analisador de consulta Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

O analisador simples é mais flexível e tentará interpretar uma solicitação mesmo que não seja perfeitamente composta. Por causa dessa flexibilidade, é o padrão para consultas no Azure Pesquisa Cognitiva. 

A sintaxe simples é usada para expressões de consulta passadas no `search` parâmetro de uma [solicitação de documentos de pesquisa](/rest/api/searchservice/search-documents), não deve ser confundida com a [sintaxe OData](query-odata-filter-orderby-syntax.md) usada para o parâmetro [$Filter Expressions](search-filters.md) da mesma API de documentos de pesquisa. Os `search` `$filter` parâmetros e têm sintaxe diferente, com suas próprias regras para construir consultas, cadeias de caracteres de escape e assim por diante.

Embora o analisador simples seja baseado na classe [analisador de consulta simples do Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , a implementação no Azure pesquisa cognitiva exclui a pesquisa difusa. Se você precisar de [pesquisa difusa](search-query-fuzzy.md) ou outros formulários de consulta avançados, considere a [sintaxe de consulta Lucene completa](query-lucene-syntax.md) alternativa em vez disso.

## <a name="invoke-simple-parsing"></a>Invocar análise simples

A sintaxe simples é o padrão. A invocação só será necessária se você redefinir a sintaxe de completa para simples. Para definir explicitamente a sintaxe, use o parâmetro de pesquisa `queryType`. Os valores válidos incluem `queryType=simple` ou `queryType=full` , onde `simple` é o padrão e `full` invoca o [analisador de consulta Lucene completo](query-lucene-syntax.md) para consultas mais avançadas. 

## <a name="syntax-fundamentals"></a> Conceitos básicos da sintaxe

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução da consulta. O Azure Pesquisa Cognitiva corresponderá a documentos que contêm qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto.

Tão simples quanto esse som, há um aspecto da execução da consulta no Azure Pesquisa Cognitiva que *pode* produzir resultados inesperados, aumentando em vez de reduzir os resultados da pesquisa conforme mais termos e operadores são adicionados à cadeia de caracteres de entrada. Se essa expansão realmente ocorrer depende da inclusão de um operador NOT, combinada com uma configuração de parâmetro **searchmode** que determina como o não é interpretado em termos de e ou ou comportamentos. Para obter mais informações, confira [Operador NOT](#not-operator).

### <a name="precedence-operators-grouping"></a>Operadores de precedência (agrupamento)

Você pode usar parênteses para criar subconsultas, incluindo operadores dentro da instrução entre parênteses. Por exemplo, `motel+(wifi|luxury)` irá procurar documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas tem como escopo o agrupamento para um único campo. Por exemplo, `hotelAmenities:(gym+(wifi|pool))` pesquisa o campo "comodidadesDoHotel" para "academia" e "wifi" ou "academia" e "piscina".  

### <a name="escaping-search-operators"></a>Operadores de escape de pesquisa  

Na sintaxe simples, os operadores de pesquisa incluem estes caracteres: `+ | " ( ) ' \`  

Se qualquer um desses caracteres fizer parte de um token no índice, faça o escape dele prefixando-o com uma barra invertida única ( `\` ) na consulta. Por exemplo, suponha que você usou um analisador personalizado para geração de tokens de termo inteiro e o índice contém a cadeia de caracteres "luxo + Hotel". Para obter uma correspondência exata desse token, insira um caractere de escape:  `search=luxury\+hotel` . 

Para tornar as coisas simples para os casos mais comuns, há duas exceções a essa regra, em que o escape não é necessário:  

+ O operador NOT `-` só precisa ser ignorado se for o primeiro caractere após um espaço em branco. Se o `-` aparecer no meio (por exemplo, em `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), você poderá ignorar a saída.

+ O operador de sufixo `*` só precisa ser ignorado se for o último caractere antes de um espaço em branco. Se o `*` aparecer no meio (por exemplo, em `4*4=16` ), nenhuma saída será necessária.

> [!NOTE]  
> Por padrão, o analisador padrão excluirá e quebrará palavras em hifens, espaço em branco, e comercial e outros caracteres durante a [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis). Se você precisar que caracteres especiais permaneçam na cadeia de caracteres de consulta, talvez seja necessário um analisador que os preserve no índice. Algumas opções incluem [analisadores de idioma](index-add-language-analyzers.md)natural da Microsoft, que preservam palavras hifenizadas ou um analisador personalizado para padrões mais complexos. Para obter mais informações, consulte [termos parciais, padrões e caracteres especiais](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de caracteres reservados e não seguros em URLs

Verifique se todos os caracteres reservados e não seguros estão codificados em uma URL. Por exemplo, ' # ' é um caractere não seguro porque é um identificador de fragmento/âncora em uma URL. O caractere deverá ser codificado como `%23` se for usado em uma URL. ' & ' e ' = ' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva do Azure. Consulte [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

Os caracteres não seguros são ``" ` < > # % { } | \ ^ ~ [ ]``. Os caracteres reservados são `; / ? : @ = + &`.

### <a name="querying-for-special-characters"></a>Consultando caracteres especiais

Em algumas circunstâncias, talvez você queira procurar um caractere especial, como o emoji ' ❤ ' ou o sinal ' € '. Nesse caso, certifique-se de que o analisador usado não filtre esses caracteres.  O analisador padrão ignora muitos dos caracteres especiais para que não se tornem tokens em seu índice.

Portanto, a primeira etapa é garantir que você use um analisador que considerará esses tokens de elementos. Por exemplo, o analisador "Whitespace" leva em consideração quaisquer sequências de caracteres separadas por espaços em branco como tokens, de modo que a cadeia de caracteres "❤" seria considerada um token. Além disso, um analisador como o Microsoft English Analyzer ("en. Microsoft"), levaria em consideração a cadeia de caracteres "€" como um token. Você pode [testar um analisador](/rest/api/searchservice/test-analyzer) para ver quais tokens ele gera para uma determinada consulta.

Ao usar caracteres Unicode, certifique-se de que os símbolos tenham um escape correto na URL de consulta (por exemplo, para "❤" usaria a sequência de escape `%E2%9D%A4+` ). O postmaster faz essa tradução automaticamente.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> Limites de tamanho de consulta

 Há um limite para o tamanho das consultas que você pode enviar para o Azure Pesquisa Cognitiva. Especificamente, você pode ter, no máximo, 1024 cláusulas (expressões separadas por AND, OR e assim por diante). Há também um limite de aproximadamente 32 KB em relação ao tamanho de qualquer termo individual em uma consulta. Se seu aplicativo gerar consultas de pesquisa por meio de programação, é recomendável criá-lo de forma que ele não gere consultas de tamanho ilimitado.  

## <a name="boolean-search"></a>Pesquisa booliana

Você pode inserir operadores boolianos (e, ou, não) em uma cadeia de caracteres de consulta para criar um conjunto avançado de critérios em relação aos quais os documentos correspondentes são encontrados. 

### <a name="and-operator-"></a>Operador AND `+`

O operador AND é um sinal de adição. Por exemplo, `wifi + luxury` procura documentos que contenham `wifi` e `luxury`.

### <a name="or-operator-"></a>Operador OR `|`

O operador OR é uma barra vertical ou um caractere de pipe. Por exemplo, `wifi | luxury` procura documentos que contenham `wifi`, `luxury` ou ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operador NOT `-`

O operador NOT é um sinal de subtração. Por exemplo, o `wifi –luxury` procurará documentos que tenham o `wifi` termo e/ou que não tenham `luxury` .

O parâmetro **searchmode** em uma solicitação de consulta controla se um termo com o operador NOT é ANDed ou orns com outros termos na consulta (supondo que não haja nenhum `+` `|` operador OR nos outros termos). Os valores válidos incluem `any` ou `all`.

`searchMode=any` aumenta a recall de consultas, incluindo mais resultados e, por padrão, `-` será interpretado como "ou não". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` ou aqueles que não contêm o termo `luxury`.

`searchMode=all` aumenta a precisão das consultas, incluindo menos resultados e, por padrão, será interpretado como "e não". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` e não contêm o termo "luxo". Esse é indiscutivelmente um comportamento mais intuitivo para o operador `-`. Portanto, você deve considerar `searchMode=all` o uso do em vez de `searchMode=any` se deseja otimizar as pesquisas de precisão em vez de recall, *e* os usuários frequentemente usam o `-` operador em pesquisas.

Ao decidir sobre uma configuração de **searchmode** , considere os padrões de interação do usuário para consultas em vários aplicativos. Os usuários que estão pesquisando informações têm mais probabilidade de incluir um operador em uma consulta, em oposição aos sites de comércio eletrônico que têm estruturas de navegação mais internas.

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>Correspondência de prefixo curinga (*,?)

Para consultas "começa com", adicione um operador de sufixo como o espaço reservado para o restante de um termo. Use um asterisco `*` para vários caracteres ou `?` para caracteres únicos. Por exemplo, `lingui*` será correspondido em "linguístico" ou "Linguini", ignorando maiúsculas e minúsculas. 

Semelhante aos filtros, uma consulta de prefixo procura uma correspondência exata. Assim, não há nenhuma pontuação de relevância (todos os resultados recebem uma pontuação de pesquisa de 1,0). Lembre-se de que as consultas de prefixo podem ser lentas, especialmente se o índice for grande e o prefixo consistir em um pequeno número de caracteres. Uma metodologia alternativa, como a geração de tokens de n-grama de borda, pode ser executada mais rapidamente.

Para outras variantes de consulta curinga, como sufixo ou correspondência de infixos em relação ao final ou ao meio de um termo, use a [sintaxe Lucene completa para pesquisa de curinga](query-lucene-syntax.md#bkmk_wildcard).

## <a name="phrase-search-"></a>Pesquisa de frases `"`

Uma pesquisa de termo é uma consulta de um ou mais termos, em que qualquer um dos termos é considerado uma correspondência. Uma pesquisa de frase é uma frase exata entre aspas `" "` . Por exemplo, while `Roach Motel` (sem aspas) pesquisaria documentos que contenham `Roach` e/ou em qualquer `Motel` lugar em qualquer ordem, `"Roach Motel"` (com aspas) só corresponderão a documentos que contenham essa frase inteira e nessa ordem (a análise léxica ainda se aplica).

## <a name="see-also"></a>Confira também  

+ [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para a pesquisa completa do Lucene](search-query-lucene-examples.md)
+ [API REST para pesquisar documentos](/rest/api/searchservice/Search-Documents)
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe de expressão do OData](query-odata-filter-orderby-syntax.md)