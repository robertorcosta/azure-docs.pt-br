---
title: Solucionar problemas de exceções do serviço Azure Cosmos DB indisponíveis
description: Saiba como diagnosticar e corrigir Azure Cosmos DB exceções de serviço indisponíveis.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870860"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnosticar e solucionar problemas de exceções do serviço Azure Cosmos DB indisponíveis
O SDK não foi capaz de se conectar ao Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções de serviço indisponíveis.

### <a name="the-required-ports-are-being-blocked"></a>As portas necessárias estão sendo bloqueadas
Verifique se todas as [portas necessárias](performance-tips-dotnet-sdk-v3-sql.md#networking) estão habilitadas.

### <a name="client-side-transient-connectivity-issues"></a>Problemas de conectividade transitório do lado do cliente
As exceções de serviço indisponíveis podem surgir quando há problemas de conectividade transitórios que estão causando tempos limite. Normalmente, o rastreamento de pilha relacionado a esse cenário conterá um `TransportException` erro. Por exemplo:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Siga as [etapas de solução de problemas de tempo limite da solicitação](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) para resolvê-lo.

### <a name="service-outage"></a>Interrupção do serviço
Verifique o [status do Azure](https://status.azure.com/status) para ver se há um problema em andamento.


## <a name="next-steps"></a>Próximas etapas
* [Diagnostique e solucione](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB.
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md).