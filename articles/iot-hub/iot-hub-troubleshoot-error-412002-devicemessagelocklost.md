---
title: Solução de problemas Erro do Hub IoT do Azure 412002 DeviceMessageLockLost
description: Entenda como corrigir o erro 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960757"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Este artigo descreve as causas e soluções para **erros 412002 DeviceMessageLockLost.**

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem nuvem-para-dispositivo, a solicitação falha com o erro **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Causa

Quando um dispositivo recebe uma mensagem nuvem-dispositivo da fila [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)(por exemplo, usando ) a mensagem é bloqueada pelo IoT Hub por um tempo de bloqueio de duração de um minuto. Se o dispositivo tentar completar a mensagem após o término do tempo de bloqueio, o IoT Hub lançará essa exceção.

## <a name="solution"></a>Solução

Se o IoT Hub não receber a notificação dentro da duração do tempo de bloqueio de um minuto, ele definirá a mensagem de volta ao estado *Enqueued.* O dispositivo pode tentar receber a mensagem novamente. Para evitar que o erro aconteça no futuro, implemente a lógica lateral do dispositivo para completar a mensagem dentro de um minuto após receber a mensagem. Esse intervalo de um minuto não pode ser mudado.