---
title: Solução de problemas Erro do Hub Azure IoT 403004 DeviceMaximumQueueDepthExted
description: Entenda como corrigir erro 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1edf723aa885ff18d2ce2dda4d71b67700a98a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497487"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Este artigo descreve as causas e soluções para **403004 DeviceMaximumQueueDepthExceeded** erros.

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem nuvem-para-dispositivo, a solicitação falha com o erro **403004** ou **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Causa

A causa básica é que o número de mensagens enfileiradas para o dispositivo excede o [limite de fila (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

A razão mais provável para você estar correndo para esse limite é porque você está `ReceiveAsync`usando HTTPS para receber a mensagem, o que leva a uma votação contínua usando , resultando em IoT Hub estrangulando a solicitação.

## <a name="solution"></a>Solução

O padrão com suporte para mensagens da nuvem para o dispositivo com o HTTPS são dispositivos conectados intermitentemente que verificam mensagens com pouca frequência (menos do que a cada 25 minutos). Para reduzir a probabilidade de correr para o limite da fila, mude para AMQP ou MQTT para mensagens nuvem-dispositivo.

Alternativamente, melhore a lógica do lado do dispositivo para completar, rejeitar ou abandonar mensagens enfileiradas rapidamente, encurtar o tempo de vida ou considerar o envio de menos mensagens. Consulte [vida útil da mensagem C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Por último, considere usar a [API da Fila de Expurgo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) para limpar periodicamente as mensagens pendentes antes que o limite seja atingido.