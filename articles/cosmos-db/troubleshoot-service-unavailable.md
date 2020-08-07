---
title: Solucionar problemas de exceção de serviço Azure Cosmos DB indisponível
description: Como diagnosticar e corrigir Cosmos DB exceção de serviço indisponível
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987368"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnosticar e solucionar problemas do serviço Azure Cosmos DB indisponível
O SDK não pôde se conectar ao serviço de Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções de serviço indisponíveis.

### <a name="1-the-required-ports-are-being-blocked"></a>1. as portas necessárias estão sendo bloqueadas
Verifique se todas as [portas necessárias](performance-tips-dotnet-sdk-v3-sql.md#networking) estão habilitadas.

### <a name="2-client-side-transient-connectivity-issues"></a>2. problemas de conectividade transitório do lado do cliente
A superfície de exceções do serviço indisponível pode surgir quando há problemas de conectividade transitórios que estão causando tempos limite. Normalmente, o rastreamento de pilha relacionado a esse cenário conterá um `TransportException` , por exemplo:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Siga a [solução de problemas de tempo limite da solicitação](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) para resolvê-lo.

### <a name="3-service-outage"></a>3. interrupção do serviço
Verifique o [status do Azure](https://status.azure.com/status) para ver se há um problema em andamento.


## <a name="next-steps"></a>Próximas etapas
* [Diagnosticar e solucionar](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md)