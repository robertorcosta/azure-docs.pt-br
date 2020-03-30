---
title: incluir arquivo
description: incluir arquivo
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198319"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Duração do tempo do tempo do aplicativo de função 

A duração do tempo de um `functionTimeout` aplicativo de função é definida pela propriedade no arquivo do projeto [host.json.](../articles/azure-functions/functions-host-json.md#functiontimeout) A tabela a seguir mostra os valores padrão e máximo em minutos para ambos os planos e as diferentes versões em tempo de execução:

| Plano | Versão em tempo de execução | Padrão | Máximo |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2. x | 5 | 10 |
| Consumo | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Ilimitado |
| Premium | 2. x | 30 | Ilimitado |
| Premium | 3.x | 30 | Ilimitado |
| Serviço de Aplicativo | 1.x | Ilimitado | Ilimitado |
| Serviço de Aplicativo | 2. x | 30 | Ilimitado |
| Serviço de Aplicativo | 3.x | 30 | Ilimitado |

> [!NOTE] 
> Independentemente da configuração de tempo limite do aplicativo de função, 230 segundos é o tempo máximo que uma função acionada HTTP pode levar para responder a uma solicitação. Isso se deve ao [tempo de espera padrão do Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Para tempos de processamento mais longos, considere usar o [padrão de assincronia funções duráveis](../articles/azure-functions/durable/durable-functions-overview.md#async-http) ou [adiar o trabalho real e retornar uma resposta imediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
