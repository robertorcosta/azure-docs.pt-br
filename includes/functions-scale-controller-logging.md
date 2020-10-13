---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169894"
---
| | |
|--|--|
|**`<DESTINATION>`**| O destino para o qual os logs são enviados. Os valores válidos são `AppInsights` e `Blob`.<br/>Ao usar `AppInsights` o, verifique se [Application insights está habilitado em seu aplicativo de funções](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>Quando você define o destino como `Blob` , os logs são criados em um contêiner de blob nomeado `azure-functions-scale-controller` na conta de armazenamento padrão definida na `AzureWebJobsStorage` configuração do aplicativo. |
|**`<VERBOSITY>`** | Especifica o nível de registro em log. Os valores com suporte são `None`, `Warning` e `Verbose`.<br/>Quando definido como `Verbose` , o controlador de escala registra um motivo para cada alteração na contagem de trabalhadores, bem como informações sobre os gatilhos que fator nessas decisões. Os logs detalhados incluem avisos de gatilho e os hashes usados pelos gatilhos antes e depois que o controlador de escala é executado. |

> [!CAUTION]
> Não deixe o registro em log do controlador de escala habilitado. Habilite o log até que você tenha coletado dados suficientes para entender como o controlador de escala está se comportando e, em seguida, desabilite-o.