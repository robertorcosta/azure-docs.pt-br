---
title: Limites do Azure Cosmos DB Gremlin
description: Documentação de referência para limitações de tempo de execução do mecanismo Graph
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029857"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limites do Gremlin do Azure Cosmos DB
Este artigo fala sobre os limites do motor Azure Cosmos DB Gremlin e explica como eles podem impactar as travessias dos clientes.

Cosmos DB Gremlin é construído em cima da infra-estrutura Cosmos DB. Devido a isso, todos os limites explicados no [Azure Cosmos DB ainda](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) se aplicam. 

## <a name="limits"></a>limites

Quando o limite de Gremlin é atingido, a travessia é cancelada com um **código de status x-ms de** 429 indicando um erro de estrangulamento. Consulte [os cabeçalhos de resposta do servidor Gremlin](gremlin-limits.md) para obter mais informações.

**Recurso**    | **Limite padrão** | **Explicação**
--- | --- | ---
*Tamanho do script* | **64 KB** | Comprimento máximo de um script de passagem do Gremlin por solicitação.
*Profundidade do operador* | **400** |  Número total de etapas exclusivas em uma passagem. Por ```g.V().out()``` exemplo, tem uma contagem de operadores ```g.V('label').repeat(out()).times(100)``` de 2: V() e out(), tem ```.times(100)``` profundidade do operador ```.repeat()``` de 3: V(), repeat() e out() porque é um parâmetro para o operador.
*Grau de paralelismo* | **32** | Número máximo de partições de armazenamento consultado em uma única solicitação para a camada de armazenamento. Gráficos com centenas de partições serão impactados por esse limite.
*Limite de repetição* | **32** | Número máximo de iterações que um operador ```.repeat()``` pode executar. Cada iteração ```.repeat()``` de passo na maioria dos casos é uma travessia de largura, o que significa que qualquer travessia é limitada a no máximo 32 lúpulos entre vértices.
*Tempo limite de passagem* | **30 segundos** | A travessia será cancelada quando exceder desta vez. O Cosmos DB Graph é um banco de dados OLTP com a grande maioria das passagens sendo concluídas em milissegundos. Para executar consultas OLAP no Cosmos DB Graph, use [o Apache Spark](https://azure.microsoft.com/services/cosmos-db/) com [quadros de dados gráficos](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) e [o conector de faísca SMS .](https://github.com/Azure/azure-cosmosdb-spark)
*Tempo limite de conexão ociosa* | **1 hora** | Quantidade de tempo que o serviço Gremlin manterá abertas as conexões ociosas de websocket. Pacotes de manutenção do TCP ou solicitações http keep-alive não estendem a vida útil da conexão além desse limite. O mecanismo Cosmos DB Graph considera que as conexões de websocket estão ociosas se não houver solicitações ativas de Gremlin em execução nele.
*Token de recurso por hora* | **100** | Número de tokens de recurso exclusivos usados pelos clientes Gremlin para se conectar à conta Gremlin em uma região. Quando o aplicativo exceder o limite `"Exceeded allowed resource token limit of 100 that can be used concurrently"` de token exclusivo por hora, será devolvido na próxima solicitação de autenticação.

## <a name="next-steps"></a>Próximas etapas
* [Cabeçalhos de resposta Azure Cosmos DB Gremlin](gremlin-headers.md) 
* [Tokens de recurso do Azure Cosmos DB com Gremlin](how-to-use-resource-tokens-gremlin.md)
