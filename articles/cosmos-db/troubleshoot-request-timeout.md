---
title: Solucionar problemas Azure Cosmos DB exceção de tempo limite de solicitação de serviço
description: Como diagnosticar e corrigir Cosmos DB exceção de tempo limite da solicitação de serviço
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293948"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Diagnosticar e solucionar problemas de Azure Cosmos DB tempo limite de solicitação
Azure Cosmos DB retornou um tempo limite de solicitação HTTP 408

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções de tempo limite de solicitação.

### <a name="1-check-the-sla"></a>1. Verifique o SLA
O cliente deve verificar o [monitoramento de Azure Cosmos DB](monitor-cosmos-db.md) para verificar se o número 408 de exceções viola o SLA de Cosmos DB.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>Solução 1: ela não violou o SLA de Cosmos DB
O aplicativo deve lidar com esse cenário e tentar novamente nessas falhas transitórias.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>Solução 2: ela violou a Cosmos DB SLA
Contate o suporte do Azure:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. chave de partição ativa
Azure Cosmos DB distribui a taxa de transferência provisionada de maneira uniforme entre as partições físicas. Quando há uma partição ativa, uma ou mais chaves de partição lógica em uma partição física estão consumindo todas as RU/s da partição física, enquanto os RU/s em outras partições físicas ficam inutilizados. Como sintoma, o total de RU/s consumido será menor do que o RU/s provisionado geral no banco de dados ou contêiner, mas você ainda verá limitação (429s) nas solicitações em relação à chave de partição lógica ativa. Use a [métrica de consumo de ru normalizada](monitor-normalized-request-units.md) para ver se a carga de trabalho está encontrando uma partição ativa. 

#### <a name="solution"></a>Solução:
Escolha uma boa chave de partição que distribua uniformemente o volume de solicitação e o armazenamento. Saiba como [alterar sua chave de partição](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Próximas etapas
* [Diagnosticar e solucionar](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md)