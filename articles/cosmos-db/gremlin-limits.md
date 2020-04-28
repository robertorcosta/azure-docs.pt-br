---
title: Limites de Azure Cosmos DB Gremlin
description: Documentação de referência para limitações de tempo de execução do mecanismo de grafo
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "72029857"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limites do Gremlin do Azure Cosmos DB
Este artigo fala sobre os limites do mecanismo de Azure Cosmos DB Gremlin e explica como eles podem afetar as passagens dos clientes.

Cosmos DB Gremlin é criado sobre a infraestrutura de Cosmos DB. Devido a isso, todos os limites explicados em [Azure Cosmos DB limites de serviço](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) ainda se aplicam. 

## <a name="limits"></a>limites

Quando o limite de Gremlin é atingido, a passagem é cancelada com um **x-MS-status-código** de 429 indicando um erro de limitação. Consulte [cabeçalhos de resposta do servidor Gremlin](gremlin-limits.md) para obter mais informações.

**Recurso**    | **Limite padrão** | **Explicação**
--- | --- | ---
*Tamanho do script* | **64 KB** | Comprimento máximo de um script de passagem do Gremlin por solicitação.
*Profundidade do operador* | **400** |  Número total de etapas exclusivas em uma passagem. Por exemplo, ```g.V().out()``` tem uma contagem de operadores de 2: v () e out () ```g.V('label').repeat(out()).times(100)``` , tem profundidade de operador de 3: v (), REPEAT () e out () ```.times(100)``` porque é um parâmetro ```.repeat()``` para operador.
*Grau de paralelismo* | **32** | Número máximo de partições de armazenamento consultado em uma única solicitação para a camada de armazenamento. Grafos com centenas de partições serão afetados por esse limite.
*Limite de repetição* | **32** | Número máximo de iterações que um operador ```.repeat()``` pode executar. Cada iteração ```.repeat()``` da etapa na maioria dos casos executa a passagem em primeiro lugar, o que significa que qualquer percurso é limitado a no máximo 32 saltos entre vértices.
*Tempo limite de passagem* | **30 segundos** | A passagem será cancelada quando exceder esse tempo. O Cosmos DB Graph é um banco de dados OLTP com a grande maioria das passagens sendo concluídas em milissegundos. Para executar consultas OLAP em Cosmos DB grafo, use [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) com [quadros de dados do grafo](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) e [Cosmos DB conector do Spark](https://github.com/Azure/azure-cosmosdb-spark).
*Tempo limite de conexão ociosa* | **1 hora** | Quantidade de tempo que o serviço Gremlin manterá conexões de WebSocket ociosas abertas. Os pacotes Keep-Alive TCP ou as solicitações de Keep-Alive HTTP não estendem a duração da conexão além desse limite. Cosmos DB mecanismo de grafo considera conexões WebSocket como ociosas se não houver nenhuma solicitação Gremlin ativa em execução nela.
*Token de recurso por hora* | **100** | Número de tokens de recurso exclusivos usados pelos clientes Gremlin para se conectar à conta Gremlin em uma região. Quando o aplicativo excede o limite de token exclusivo por `"Exceeded allowed resource token limit of 100 that can be used concurrently"` hora, será retornado na próxima solicitação de autenticação.

## <a name="next-steps"></a>Próximas etapas
* [Azure Cosmos DB cabeçalhos de resposta do Gremlin](gremlin-headers.md) 
* [Tokens de recurso do Azure Cosmos DB com Gremlin](how-to-use-resource-tokens-gremlin.md)
