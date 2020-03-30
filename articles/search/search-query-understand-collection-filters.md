---
title: Entendendo os filtros de coleta de OData
titleSuffix: Azure Cognitive Search
description: Aprenda a mecânica de como os filtros de coleta oData funcionam nas consultas de pesquisa cognitiva do Azure, incluindo limitações e comportamentos exclusivos para coleções.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113059"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Entendendo os filtros de coleta de OData na Pesquisa Cognitiva do Azure

Para [filtrar](query-odata-filter-orderby-syntax.md) os campos de coleta no Azure Cognitive Search, você pode usar os [ `any` operadores e `all` operadores](search-query-odata-collection-operators.md) juntamente com **expressões lambda**. Expressões lambda são expressões booleanas que se referem a uma **variável de intervalo**. Os `any` `all` operadores e operadores `for` são análogos a um loop na maioria das linguagens de programação, com a variável de alcance assumindo o papel da variável loop, e a expressão lambda como o corpo do loop. A variável de intervalo assume o valor "atual" da coleção durante a iteração do loop.

Pelo menos é assim que funciona conceitualmente. Na realidade, o Azure Cognitive Search implementa filtros de uma maneira muito diferente de como `for` os loops funcionam. Idealmente, essa diferença seria invisível para você, mas em certas situações não é. O resultado final é que existem regras que você tem que seguir ao escrever expressões lambda.

Este artigo explica por que as regras para filtros de coleta existem explorando como o Azure Cognitive Search executa esses filtros. Se você está escrevendo filtros avançados com expressões lambda complexas, você pode achar este artigo útil na construção de sua compreensão do que é possível em filtros e por quê.

Para obter informações sobre quais são as regras para filtros de coleta, incluindo exemplos, consulte filtros de [coleta de oData solucionando problemas na Pesquisa Cognitiva do Azure](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Por que os filtros de coleta são limitados

Existem três razões subjacentes pelas quais nem todos os recursos do filtro são suportados para todos os tipos de coleções:

1. Apenas certos operadores são suportados para determinados tipos de dados. Por exemplo, não faz sentido comparar os `true` valores `lt` `gt`booleanos e `false` usar , e assim por diante.
1. A Pesquisa Cognitiva do Azure não suporta `Collection(Edm.ComplexType)` **pesquisa correlacionada** em campos do tipo .
1. O Azure Cognitive Search usa índices invertidos para executar filtros sobre todos os tipos de dados, incluindo coleções.

A primeira razão é apenas uma consequência de como a linguagem OData e o sistema de tipo EDM são definidos. Os dois últimos são explicados com mais detalhes no resto deste artigo.

## <a name="correlated-versus-uncorrelated-search"></a>Pesquisa correlacionada versus não correlacionada

Ao aplicar vários critérios de filtro sobre uma coleção de objetos complexos, os critérios estão **correlacionados,** uma vez que se aplicam a *cada objeto na coleção.* Por exemplo, o seguinte filtro retornará hotéis que possuem pelo menos um quarto de luxo com uma taxa menor que 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Se a filtragem não fosse *correlacionada,* o filtro acima poderia retornar hotéis onde um quarto é de luxo e um quarto diferente tem uma taxa básica menor que 100. Isso não faria sentido, uma vez que ambas as cláusulas da `room`expressão lambda aplicam-se à mesma variável de faixa, ou seja. É por isso que esses filtros estão correlacionados.

No entanto, para pesquisa de texto completo, não há como se referir a uma variável de intervalo específica. Se você usar a pesquisa em campo para emitir uma [consulta completa de Lucene](query-lucene-syntax.md) como esta:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

você pode obter hotéis de volta onde um quarto é de luxo, e um quarto diferente menciona "vista da cidade" na descrição. Por exemplo, o `Id` documento `1` abaixo com o de seria compatível com a consulta:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

A razão `Rooms/Type` é que se refere a `Rooms/Type` todos os termos analisados do `Rooms/Description`campo em todo o documento, e da mesma forma para , como mostrado nas tabelas abaixo.

Como `Rooms/Type` é armazenado para pesquisa de texto completo:

| Termo em`Rooms/Type` | IDs de documentos |
| --- | --- |
| Deluxe | 1, 2 |
| padrão | 1 |

Como `Rooms/Description` é armazenado para pesquisa de texto completo:

| Termo em`Rooms/Description` | IDs de documentos |
| --- | --- |
| Pátio | 2 |
| city | 1 |
| Jardim | 1 |
| grande | 1 |
| motel | 2 |
| sala | 1, 2 |
| padrão | 1 |
| Suite | 1 |
| exibição | 1 |

Assim, ao contrário do filtro acima, que basicamente `Type` diz "combinar documentos onde uma `BaseRate` sala tem igual a 'Sala Deluxe' `Rooms/Type` e que a mesma `Rooms/Description` **sala** tem menos de 100", a consulta de pesquisa diz "combinar documentos onde tem o termo "deluxe" e tem a frase "vista da cidade". Não há conceito de salas individuais cujos campos podem ser correlacionados neste último caso.

> [!NOTE]
> Se você quiser ver o suporte para pesquisa correlacionada adicionada à Pesquisa Cognitiva do Azure, por favor, vote [neste item User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Índices e coleções invertidas

Você deve ter notado que há muito menos restrições às expressões lambda sobre `Collection(Edm.Int32)` `Collection(Edm.GeographyPoint)`coleções complexas do que há para coleções simples como , e assim por diante. Isso ocorre porque o Azure Cognitive Search armazena coleções complexas como coleções reais de subdocumentos, enquanto coleções simples não são armazenadas como coleções.

Por exemplo, considere um campo `seasons` de coleta de strings filtrado como em um índice para um varejista online. Alguns documentos carregados para este índice podem ser assim:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

Os valores `seasons` do campo são armazenados em uma estrutura chamada **índice invertido,** que se parece com algo assim:

| Termo | IDs de documentos |
| --- | --- |
| Primavera | 1, 2 |
| Verão | 1 |
| Queda | 1, 2 |
| Inverno | 2, 3 |

Essa estrutura de dados foi projetada para responder a uma pergunta com grande velocidade: Em quais documentos aparece um determinado termo? Responder a essa pergunta funciona mais como uma verificação de igualdade simples do que um loop sobre uma coleção. Na verdade, é por isso que para coleções de `eq` cordas, o Azure `any`Cognitive Search só permite como um operador de comparação dentro de uma expressão lambda para .

Acumulando-se a partir da igualdade, em seguida, vamos ver como é possível `or`combinar múltiplas verificações de igualdade na mesma variável de intervalo com . Funciona graças à álgebra e [à propriedade distributiva dos quantificadores.](https://en.wikipedia.org/wiki/Existential_quantification#Negation) Esta expressão:

    seasons/any(s: s eq 'winter' or s eq 'fall')

é equivalente a:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

e cada uma `any` das duas subexpressões pode ser executada eficientemente usando o índice invertido. Além disso, graças à [lei de negação dos quantificadores,](https://en.wikipedia.org/wiki/Existential_quantification#Negation)esta expressão:

    seasons/all(s: s ne 'winter' and s ne 'fall')

é equivalente a:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

é por isso que é `all` `ne` possível `and`usar com e .

> [!NOTE]
> Embora os detalhes estejam além do escopo deste documento, esses mesmos princípios se estendem a [testes de distância e intersecção para coletas de pontos geoespaciais](search-query-odata-geo-spatial-functions.md) também. É por isso `any`que, em:
>
> - `geo.intersects`não pode ser negado
> - `geo.distance`deve ser `lt` comparado usando ou`le`
> - expressões devem ser `or`combinadas com , não`and`
>
> As regras inversas se aplicam a `all`.

Uma variedade mais ampla de expressões é permitida ao filtrar `le`em `ge` coleções de tipos `Collection(Edm.Int32)` de dados que suportam os `lt` `gt`, e operadores, como por exemplo. Especificamente, você `and` `or` pode usar, `any`bem como em , desde que as expressões de comparação subjacentes sejam `or`combinadas em **comparações de intervalo** usando `and`, que são então mais combinadas usando . Esta estrutura de expressões booleanas é chamada [de Forma Normal Disjuntiva (DNF),](https://en.wikipedia.org/wiki/Disjunctive_normal_form)também conhecida como "ORs de ANDs". Por outro lado, as `all` expressões lambda para esses tipos de dados devem estar na [Forma Normal Conjuntiva (CNF),](https://en.wikipedia.org/wiki/Conjunctive_normal_form)também conhecida como "ANDs de ORs". O Azure Cognitive Search permite tais comparações de intervalo porque pode executá-los usando índices invertidos de forma eficiente, assim como pode fazer pesquisa rápida de prazo para strings.

Em resumo, aqui estão as regras do polegar para o que é permitido em uma expressão lambda:

- No `any`interior, *verificações positivas* são sempre permitidas, como igualdade, comparações de intervalo, `geo.intersects`ou `geo.distance` comparadas com `lt` ou `le` (pense em "proximidade" como sendo igualdade quando se trata de verificar a distância).
- Por `any` `or` dentro, é sempre permitido. Você só `and` pode usar para tipos de dados que podem expressar verificações de faixa, e somente se você usar ORs de ANDs (DNF).
- No `all`interior, as regras são invertidas - apenas `and` *verificações negativas* são permitidas, você pode usar sempre, e você pode usar `or` apenas para verificações de intervalo expressas como ANDs de ORs (CNF).

Na prática, estes são os tipos de filtros que você é mais provável de usar de qualquer maneira. Ainda é útil entender os limites do que é possível.

Para exemplos específicos de quais tipos de filtros são permitidos e quais não são, consulte [Como escrever filtros de coleta válidos](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Próximas etapas  

- [Solução de problemas Filtros de coleta de OData na Pesquisa Cognitiva do Azure](search-query-troubleshoot-collection-filters.md)
- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
