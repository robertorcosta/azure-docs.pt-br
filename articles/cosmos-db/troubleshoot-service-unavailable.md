---
title: Solucionar problemas de exceção de serviço Azure Cosmos DB indisponível
description: Como diagnosticar e corrigir Cosmos DB exceção de serviço indisponível
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293946"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnosticar e solucionar problemas do serviço Azure Cosmos DB indisponível
O SDK não pôde se conectar ao serviço de Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções de serviço indisponíveis.

### <a name="1-the-required-ports-are-not-enabled"></a>1. as portas necessárias não estão habilitadas.
Verifique se todas as [portas necessárias](performance-tips-dotnet-sdk-v3-sql.md#networking) estão habilitadas.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Se um aplicativo ou serviço existente começou a obter 503

### <a name="1-there-is-an-outage"></a>1. há uma interrupção
Verifique o [status do Azure](https://status.azure.com/status) para ver se há um problema em andamento.

### <a name="2-snat-port-exhaustion"></a>2. esgotamento de porta SNAT
Siga o [Guia de esgotamento de porta SNAT](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. as portas necessárias estão sendo bloqueadas
Verifique se todas as [portas necessárias](performance-tips-dotnet-sdk-v3-sql.md#networking) estão habilitadas.

## <a name="next-steps"></a>Próximas etapas
* [Diagnosticar e solucionar](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md)