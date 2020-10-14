---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68881365"
---
#### <a name="built-in-log-streaming"></a>Streaming de log interno

Use a opção `logstream` para começar a receber logs de streaming de um aplicativo de funções específico em execução no Azure, como no exemplo a seguir:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Você também pode exibir o [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) para seu aplicativo de funções em uma nova janela do navegador, incluindo a opção `--browser`, como no exemplo a seguir:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
