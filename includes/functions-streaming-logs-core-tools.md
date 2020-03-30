---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881365"
---
#### <a name="built-in-log-streaming"></a>Streaming de log incorporado

Use `logstream` a opção para começar a receber logs de streaming de um aplicativo de função específico em execução no Azure, como no exemplo a seguir:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Você também pode visualizar o [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) para o `--browser` seu aplicativo de função em uma nova janela do navegador, incluindo a opção, como no exemplo a seguir:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
