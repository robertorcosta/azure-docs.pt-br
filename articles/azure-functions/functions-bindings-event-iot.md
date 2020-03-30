---
title: Vinculações do Azure IoT Hub para funções do Azure
description: Aprenda a usar o gatilho e a vinculação do IoT Hub em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586115"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Vinculações do Azure IoT Hub para funções do Azure

Este conjunto de artigos explica como trabalhar com as vinculações de funções do Azure para o IoT Hub. O suporte ao IoT Hub é baseado no [Azure Event Hubs Binding](functions-bindings-event-hubs.md).

> [!IMPORTANT]
> Embora as seguintes amostras de código usem a API do Event Hub, a sintaxe dada é aplicável para funções do IoT Hub.

| Ação | Type |
|--------|------|
| Responda a eventos enviados para um fluxo de eventos do hub ioT. | [Gatilho](./functions-bindings-event-iot-trigger.md) |
| Escreva eventos para um fluxo de eventos IoT | [Vinculação de saída](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Próximas etapas

- [Responder a eventos enviados para um fluxo de eventos do hub de eventos (Trigger)](./functions-bindings-event-iot-trigger.md)
- [Gravar eventos em um fluxo de eventos (vinculação de saída)](./functions-bindings-event-iot-output.md)
