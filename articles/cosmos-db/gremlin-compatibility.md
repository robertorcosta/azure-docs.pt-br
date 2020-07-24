---
title: Azure Cosmos DB a compatibilidade do Gremlin com os recursos do TinkerPop
description: Documentação de referência problemas de compatibilidade do mecanismo grafo
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 7257246f618e3028534f3ebd60eaf6f94a3a4720
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092500"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB compatibilidade com o Gremlin
Azure Cosmos DB mecanismo de grafo segue a especificação de etapas de passagem do [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , mas há diferenças na implementação que são específicas para Azure Cosmos DB. Para saber a lista de etapas de Gremlin com suporte, consulte o artigo [suporte de protocolo de transmissão de API do Gremlin](gremlin-support.md) .

## <a name="behavior-differences"></a>Diferenças de comportamento

* Azure Cosmos DB mecanismo de grafo executa uma passagem ***de primeira amplitude*** enquanto TinkerPop Gremlin é de profundidade. Esse comportamento atinge um desempenho melhor em sistema horizontalmente escalonável como Cosmos DB. 

## <a name="unsupported-features"></a>Recursos sem suporte

* O ***[Código de bytes do Gremlin](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** é uma especificação independente da linguagem de programação para passagens de gráfico. Cosmos DB grafo ainda não dá suporte a ele. Use `GremlinClient.SubmitAsync()` e passe passagem como uma cadeia de texto.

* ***`property(set, 'xyz', 1)`*** Não há suporte para definir a cardinalidade hoje. Use `property(list, 'xyz', 1)` em vez disso. Para saber mais, confira [Propriedades de vértice com TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* A *** `match()` etapa*** não está disponível no momento. Esta etapa fornece recursos de consulta declarativa.

* Não há suporte para ***objetos como propriedades*** em vértices ou bordas. As propriedades somente podem ser tipos primitivos ou matrizes.

* ***Classificando por Propriedades*** `order().by(<array property>)` de matriz Não tem suporte. É possível classifica apenas por tipos primitivos.

* Não há suporte para ***tipos JSON não primitivos*** . Use `string` os `number` tipos, ou `true` / `false` . `null`Não há suporte para valores. 

* Atualmente, o serializador ***GraphSONv3*** não tem suporte. Use `GraphSONv2` as classes de serializador, leitor e gravador na configuração de conexão. Os resultados retornados pelo Azure Cosmos DB API Gremlin não têm o mesmo formato que o formato GraphSON. 

* Atualmente, não há suporte para **expressões lambda e funções** . Isso inclui as `.map{<expression>}` funções, `.by{<expression>}` e `.filter{<expression>}` . Para saber mais e saber como reescrevê-los usando as etapas do Gremlin, consulte [uma observação sobre lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***As transações*** não têm suporte devido à natureza distribuída do sistema.  Configure o modelo de consistência apropriado na conta do Gremlin para "ler suas próprias gravações" e use a simultaneidade otimista para resolver as gravações conflitantes.

## <a name="known-limitations"></a>Limitações conhecidas

* **Utilização de índice para consultas Gremlin com `.V()` etapas de meio percurso**: atualmente, somente a primeira `.V()` chamada de passagem fará uso do índice para resolver quaisquer filtros ou predicados anexados a ele. As chamadas subsequentes não consultarão o índice, o que pode aumentar a latência e o custo da consulta.
    
    Supondo que a indexação padrão, uma consulta Gremlin de leitura típica que começa com a `.V()` etapa usaria parâmetros em suas etapas de filtragem anexadas, como `.has()` ou `.where()` para otimizar o custo e o desempenho da consulta. Por exemplo:

    ```java
    g.V().has('category', 'A')
    ```

    No entanto, quando mais de uma `.V()` etapa é incluída na consulta Gremlin, a resolução dos dados para a consulta pode não ser ideal. Veja a seguinte consulta como exemplo:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    Essa consulta retornará dois grupos de vértices com base em sua propriedade chamada `category` . Nesse caso, somente a primeira chamada `g.V().has('category', 'A')` fará uso do índice para resolver os vértices com base nos valores de suas propriedades.

    Uma solução alternativa para essa consulta é usar etapas de subpercurso, como `.map()` e `union()` . Isso é exemplificado abaixo:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    Você pode examinar o desempenho das consultas usando a [ `executionProfile()` etapa Gremlin](graph-execution-profile.md).

## <a name="next-steps"></a>Próximas etapas
* Visite [Cosmos DB](https://feedback.azure.com/forums/263030-azure-cosmos-db) página de voz de usuário para compartilhar comentários e ajudar a equipe a se concentrar em recursos que são importantes para você.
