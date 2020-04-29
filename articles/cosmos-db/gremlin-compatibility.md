---
title: Azure Cosmos DB a compatibilidade do Gremlin com os recursos do TinkerPop
description: Documentação de referência problemas de compatibilidade do mecanismo grafo
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81449877"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB compatibilidade com o Gremlin
Azure Cosmos DB mecanismo de grafo segue a especificação de etapas de passagem do [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , mas há diferenças.

## <a name="behavior-differences"></a>Diferenças de comportamento

* Azure Cosmos DB mecanismo de grafo executa uma passagem ***de primeira amplitude*** enquanto TinkerPop Gremlin é de profundidade. Esse comportamento atinge um desempenho melhor em sistema horizontalmente escalonável como Cosmos DB. 

## <a name="unsupported-features"></a>Recursos sem suporte

* O ***[Código de bytes do Gremlin](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** é uma especificação independente da linguagem de programação para passagens de gráfico. Cosmos DB grafo ainda não dá suporte a ele. Use `GremlinClient.SubmitAsync()` e passe passagem como uma cadeia de texto.

* ***`property(set, 'xyz', 1)`*** Não há suporte para definir a cardinalidade hoje. Use `property(list, 'xyz', 1)` em vez disso. Para saber mais, confira [Propriedades de vértice com TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** permite consultar grafos usando correspondência de padrão declarativa. Essa funcionalidade não está disponível.

* Não há suporte para ***objetos como propriedades*** em vértices ou bordas. As propriedades somente podem ser tipos primitivos ou matrizes.

* Não há suporte para `order().by(<array property>)` ***a classificação por propriedades de matriz*** . É possível classifica apenas por tipos primitivos.

* Não há suporte para ***tipos JSON não primitivos*** . Use `string`os `number`tipos, `true` / `false` ou. `null`Não há suporte para valores. 

* Atualmente, o serializador ***GraphSONv3*** não tem suporte. Use `GraphSONv2` as classes de serializador, leitor e gravador na configuração de conexão. Os resultados retornados pelo Azure Cosmos DB API Gremlin não têm o mesmo formato que o formato GraphSON. 

* Atualmente, não há suporte para **expressões lambda e funções** . Isso inclui as `.map{<expression>}` `.filter{<expression>}` funções, `.by{<expression>}`e. Para saber mais e saber como reescrevê-los usando as etapas do Gremlin, consulte [uma observação sobre lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***As transações*** não têm suporte devido à natureza distribuída do sistema.  Configure o modelo de consistência apropriado na conta do Gremlin para "ler suas próprias gravações" e use a simultaneidade otimista para resolver as gravações conflitantes.

## <a name="next-steps"></a>Próximas etapas
* Visite [Cosmos DB](https://feedback.azure.com/forums/263030-azure-cosmos-db) página de voz de usuário para compartilhar comentários e ajudar a equipe a se concentrar em recursos que são importantes para você.
