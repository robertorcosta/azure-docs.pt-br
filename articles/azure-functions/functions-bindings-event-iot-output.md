---
title: Vinculação de saída do Hub Azure IoT para funções do Azure
description: Aprenda a escrever mensagens para fluxos azure IoT Hubs usando funções do Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277421"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Vinculação de saída do Hub Azure IoT para funções do Azure

Este artigo explica como trabalhar com as vinculações de saída do Azure Functions para o IoT Hub. O suporte ao IoT Hub é baseado no [Azure Event Hubs Binding](functions-bindings-event-hubs.md).

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Embora as seguintes amostras de código usem a API do Event Hub, a sintaxe dada é aplicável para funções do IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Próximas etapas

- [Responder a eventos enviados para um fluxo de eventos do hub de eventos (Trigger)](./functions-bindings-event-iot-trigger.md)
