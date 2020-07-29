---
title: Solucionar problemas Azure Cosmos DB taxa de solicitação para exceção grande
description: Como diagnosticar e corrigir a taxa de solicitação para uma exceção grande
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293947"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Diagnosticar e solucionar problemas Cosmos DB muitas solicitações
' Taxa de solicitação muito grande ' ou o código de erro 429 indica que suas solicitações estão sendo limitadas.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para muitas solicitações.

### <a name="1-check-the-metrics"></a>1. Verifique as métricas
O cliente deve verificar o [monitoramento de Azure Cosmos DB](monitor-cosmos-db.md) para verificar se o número 429 exceções.

## <a name="cause"></a>Causa:
A taxa de transferência consumida (RU/s) excedeu a [taxa de transferência provisionada](set-throughput.md). O SDK tentará automaticamente as solicitações com base na política de repetição especificada. Se você receber essa falha com frequência, considere aumentar a taxa de transferência na coleção. Verifique as métricas do portal para ver se você está recebendo erros 429. Examine sua chave de partição para garantir que ela resulte em uma [distribuição uniforme do armazenamento e do volume de solicitação](partition-data.md).

## <a name="solution"></a>Solução:
1. Use o [portal ou o SDK](set-throughput.md) para aumentar a taxa de transferência provisionada.
2. Alterne o banco de dados ou o contêiner para [dimensionamento automático](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Próximas etapas
* [Diagnosticar e solucionar](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md)