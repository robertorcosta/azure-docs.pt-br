---
title: Use o perfil de execução para avaliar consultas na API Azure Cosmos DB Gremlin
description: Aprenda a solucionar problemas e melhorar suas consultas de Gremlin usando a etapa do perfil de execução.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441844"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Como usar a etapa de perfil de execução para avaliar suas consultas do Gremlin

Este artigo fornece uma visão geral de como usar a etapa de perfil de execução para bancos de dados de grafo da API Gremlin do Azure Cosmos DB. Esta etapa fornece informações relevantes para solução de problemas e otimizações de consulta e é compatível com qualquer consulta Gremlin que possa ser executada em uma conta de API do Gremlin do Cosmos DB.

Para usar esta etapa, `executionProfile()` basta anexar a chamada de função no final da consulta gremlin. **Sua consulta gremlin será executada** e o resultado da operação retornará um objeto de resposta JSON com o perfil de execução de consulta.

Por exemplo: 

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Depois de `executionProfile()` chamar a etapa, a resposta será um objeto JSON que inclui a etapa de Gremlin executada, o tempo total que levou e uma matriz de operadores de tempo de execução do Cosmos DB que a declaração resultou.

> [!NOTE]
> Esta implementação para Perfil de Execução não está definida na especificação Apache Tinkerpop. É específico para a implementação da API Azure Cosmos DB Gremlin.


## <a name="response-example"></a>Exemplo de resposta

A seguir, um exemplo anotado da saída que será devolvida:

> [!NOTE]
> Este exemplo é anotado com comentários que explicam a estrutura geral da resposta. Uma resposta real de perfil de execução não conterá comentários.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> A etapa executionProfile executará a consulta Gremlin. Isso inclui `addV` as `addE`etapas, que resultarão na criação e comprometerão as alterações especificadas na consulta. Como resultado, as Unidades de Solicitação geradas pela consulta Gremlin também serão cobradas.

## <a name="execution-profile-response-objects"></a>Objetos de resposta do perfil de execução

A resposta de uma função executionProfile() produzirá uma hierarquia de objetos JSON com a seguinte estrutura:
  - **Objeto de operação Gremlin:** Representa toda a operação Gremlin que foi executada. Contém as seguintes propriedades.
    - `gremlin`: A declaração explícita de Gremlin que foi executada.
    - `totalTime`: O tempo, em milissegundos, que a execução da etapa incorreu. 
    - `metrics`: Uma matriz que contém cada um dos operadores de tempo de execução do Cosmos DB que foram executados para cumprir a consulta. Esta lista está classificada por ordem de execução.
    
  - **Operadores de tempo de execução cosmos DB**: Representa cada um dos componentes de toda a operação Gremlin. Esta lista está classificada por ordem de execução. Cada objeto contém as seguintes propriedades:
    - `name`: Nome do operador. Este é o tipo de etapa que foi avaliada e executada. Leia mais na tabela abaixo.
    - `time`: Quantidade de tempo, em milissegundos, que um determinado operador levou.
    - `annotations`: Contém informações adicionais, específicas para o operador que foi executado.
    - `annotations.percentTime`: Porcentagem do tempo total necessário para executar o operador específico.
    - `counts`: Número de objetos que foram devolvidos da camada de armazenamento por este operador. Isso está contido `counts.resultCount` no valor escalar dentro.
    - `storeOps`: Representa uma operação de armazenamento que pode abranger uma ou várias partições.
    - `storeOps.fanoutFactor`: Representa o número de partições que esta operação de armazenamento específica acessada.
    - `storeOps.count`: Representa o número de resultados que esta operação de armazenamento retornou.
    - `storeOps.size`: Representa o tamanho em bytes do resultado de uma determinada operação de armazenamento.

Operador de tempo de execução Cosmos DB Gremlin|Descrição
---|---
`GetVertices`| Esta etapa obtém um conjunto predicado de objetos a partir da camada de persistência. 
`GetEdges`| Esta etapa obtém as bordas adjacentes a um conjunto de vértices. Esta etapa pode resultar em uma ou muitas operações de armazenamento.
`GetNeighborVertices`| Esta etapa obtém os vértices que estão conectados a um conjunto de bordas. As bordas contêm as teclas de partição e os ID's de seus vértices de origem e alvo.
`Coalesce`| Esta etapa explica a avaliação de `coalesce()` duas operações sempre que a etapa gremlin é executada.
`CartesianProductOperator`| Esta etapa calcula um produto cartesiano entre dois conjuntos de dados. Geralmente executado sempre que os `to()` `from()` predicados ou são usados.
`ConstantSourceOperator`| Esta etapa computa uma expressão para produzir um valor constante como resultado.
`ProjectOperator`| Esta etapa prepara e serializa uma resposta usando o resultado de operações anteriores.
`ProjectAggregation`| Esta etapa prepara e serializa uma resposta para uma operação agregada.

> [!NOTE]
> Esta lista continuará a ser atualizada à medida que novos operadores forem adicionados.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Exemplos de como analisar uma resposta de perfil de execução

A seguir estão exemplos de otimizações comuns que podem ser detectadas usando a resposta do Perfil de Execução:
  - Consulta cega de fan-out.
  - Consulta não filtrada.

### <a name="blind-fan-out-query-patterns"></a>Padrões cegos de consulta de fan-out

Suponha que a seguinte resposta de perfil de execução a partir de um **gráfico particionado**:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

As seguintes conclusões podem ser feitas a partir dele:
- A consulta é uma única consulta de ID, uma vez `g.V('id')`que a declaração gremlin segue o padrão .
- A julgar `time` pela métrica, a latência desta consulta parece ser alta, uma vez que é [mais de 10ms para uma única operação de leitura de ponto](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Se olharmos `storeOps` para o objeto, `fanoutFactor` podemos `5`ver que o is , o que significa que [5 partições](https://docs.microsoft.com/azure/cosmos-db/partition-data) foram acessadas por esta operação.

Como conclusão desta análise, podemos determinar que a primeira consulta está acessando mais partições do que o necessário. Isso pode ser resolvido especificando a chave de particionamento na consulta como um predicado. Isso levará a menos latência e menos custo por consulta. Saiba mais sobre o [particionamento de grafo](graph-partitioning.md). Uma consulta mais ideal `g.V('tt0093640').has('partitionKey', 't1001')`seria .

### <a name="unfiltered-query-patterns"></a>Padrões de consulta não filtrados

Compare as duas respostas a seguir do perfil de execução. Para simplificar, esses exemplos usam um único gráfico particionado.

Esta primeira consulta recupera todos os vértices com o rótulo `tweet` e, em seguida, obtém seus vértices vizinhos:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Observe o perfil da mesma consulta, mas agora `has('lang', 'en')`com um filtro adicional, antes de explorar os vértices adjacentes:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Essas duas consultas atingiram o mesmo resultado, no entanto, a primeira exigirá mais Unidades de Solicitação, uma vez que precisava iterar um conjunto de dados inicial maior antes de consultar os itens adjacentes. Podemos ver indicadores desse comportamento ao comparar os seguintes parâmetros de ambas as respostas:
- O `metrics[0].time` valor é maior na primeira resposta, o que indica que esse único passo demorou mais para ser resolvido.
- O `metrics[0].counts.resultsCount` valor é maior também na primeira resposta, o que indica que o conjunto inicial de dados de trabalho foi maior.

## <a name="next-steps"></a>Próximas etapas
* Conheça os [recursos suportados do Gremlin](gremlin-support.md) no Azure Cosmos DB. 
* Saiba mais sobre a [API Gremlin no Azure Cosmos DB](graph-introduction.md).
