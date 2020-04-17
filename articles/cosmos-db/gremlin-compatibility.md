---
title: Compatibilidade do Azure Cosmos DB Gremlin com recursos do TinkerPop
description: Documentação de referência Problemas de compatibilidade do mecanismo de gráfico
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449877"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilidade com o Azure Cosmos DB Gremlin
O motor Azure Cosmos DB Graph segue de perto a especificação de passos transversais [do Apache TinkerPop,](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) mas há diferenças.

## <a name="behavior-differences"></a>Diferenças de comportamento

* O motor Azure Cosmos DB Graph executa ***a*** primeira travessia, enquanto o TinkerPop Gremlin é o primeiro em profundidade. Esse comportamento consegue melhor desempenho em sistemas horizontalmente escaláveis como o Cosmos DB. 

## <a name="unsupported-features"></a>Recursos sem suporte

* O ***[Código de bytes do Gremlin](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** é uma especificação independente da linguagem de programação para passagens de gráfico. Cosmos DB Graph ainda não o suporta. Use `GremlinClient.SubmitAsync()` e passe a travessia como uma seqüência de texto.

* ***`property(set, 'xyz', 1)`*** definir cardinalidade não é suportado hoje. Use `property(list, 'xyz', 1)` em vez disso. Para saber mais, consulte [as propriedades da Vertex com o TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** permite consultar gráficos usando correspondência de padrão declarativo. Esta capacidade não está disponível.

* ***Objetos como propriedades*** em vértices ou bordas não são suportados. As propriedades somente podem ser tipos primitivos ou matrizes.

* ***A classificação por propriedades*** `order().by(<array property>)` de matriz não é suportada. É possível classifica apenas por tipos primitivos.

* ***Tipos JSON não primitivos*** não são suportados. Use `string` `number`, `true` / `false` ou tipos. `null`valores não são suportados. 

* O serializador ***GraphSONv3*** não é suportado no momento. Use `GraphSONv2` classes Serializer, Reader e Writer na configuração de conexão. Os resultados devolvidos pela API Azure Cosmos DB Gremlin não têm o mesmo formato do formato GraphSON. 

* **Expressões e funções lambda** não são suportadas no momento. Isso inclui `.map{<expression>}`as `.by{<expression>}`funções `.filter{<expression>}` e as funções. Para saber mais e aprender a reescrevê-los usando passos de Gremlin, consulte [Uma nota sobre Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***As transações*** não são suportadas por causa da natureza distribuída do sistema.  Configure o modelo de consistência apropriado na conta Gremlin para "ler suas próprias gravações" e use uma concorrência otimista para resolver gravações conflitantes.

## <a name="next-steps"></a>Próximas etapas
* Visite a página [de voz do usuário cosmos DB](https://feedback.azure.com/forums/263030-azure-cosmos-db) para compartilhar feedback e ajudar a equipe a se concentrar em recursos que são importantes para você.
