---
title: Solução de problemas do Hub IoT do Azure erro 412002 DeviceMessageLockLost
description: Entenda como corrigir o erro 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960757"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Este artigo descreve as causas e soluções para erros de **412002 DeviceMessageLockLost** .

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem da nuvem para o dispositivo, a solicitação falha com o erro **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Causa

Quando um dispositivo recebe uma mensagem da nuvem para o dispositivo da fila (por exemplo, usando [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) ), a mensagem é bloqueada pelo Hub IOT por uma duração de tempo limite de bloqueio de um minuto. Se o dispositivo tentar concluir a mensagem depois que o tempo limite de bloqueio expirar, o Hub IoT lançará essa exceção.

## <a name="solution"></a>Solução

Se o Hub IoT não obtiver a notificação dentro da duração do tempo limite de bloqueio de um minuto, ele definirá a mensagem de volta para o estado *enfileirado* . O dispositivo pode tentar receber a mensagem novamente. Para evitar que o erro ocorra no futuro, implemente a lógica do lado do dispositivo para concluir a mensagem dentro de um minuto após o recebimento da mensagem. Esse tempo limite de um minuto não pode ser alterado.