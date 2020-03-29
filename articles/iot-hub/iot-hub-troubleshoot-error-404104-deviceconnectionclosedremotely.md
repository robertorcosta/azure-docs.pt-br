---
title: Solução de problemas Erro do Hub IoT do Azure 404104 Conexão de dispositivofechadaremotamente
description: Entenda como corrigir o erro 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 686946b65b4b7540f404a291c87c5ad9c7b7a0ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960796"
---
# <a name="404104-deviceconnectionclosedremotely"></a>DeviceConnectionClosedRemotely 404104

Este artigo descreve as causas e soluções para **404104 DeviceConnectionClosedRemotely** erros.

## <a name="symptoms"></a>Sintomas

### <a name="symptom-1"></a>Sintoma 1

Os dispositivos se desconectam em um intervalo regular (a cada 65 minutos, por exemplo) e você vê **404104 DeviceConnectionClosedRemotamente** em registros de diagnóstico do IoT Hub. Às vezes, você também vê **401003 IoTHubUnauthorized** e um evento de conexão de dispositivo bem-sucedido menos de um minuto depois.

### <a name="symptom-2"></a>Sintoma 2

Os dispositivos se desconectam aleatoriamente, e você vê **404104 DeviceConnectionClosedRemotely** em registros de diagnóstico do IoT Hub.

### <a name="symptom-3"></a>Sintoma 3

Muitos dispositivos se desconectam ao mesmo tempo, você vê um mergulho na métrica dos [dispositivos conectados](iot-hub-metrics.md), e há mais **404104 DeviceConnectionClosedRemotely** e [500xxx Erros internos](iot-hub-troubleshoot-error-500xxx-internal-errors.md) em registros de diagnóstico do que o habitual.

## <a name="causes"></a>Causas

### <a name="cause-1"></a>Causa 1

O [token SAS usado para se conectar ao IoT Hub](iot-hub-devguide-security.md#security-tokens) expirou, o que faz com que o IoT Hub desconecte o dispositivo. A conexão é restabelecida quando o token é atualizado pelo dispositivo. Por exemplo, [o token SAS expira a cada hora por padrão para C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), o que pode levar a desconexões regulares.

Para saber mais, consulte [401003 IoTHubUnauthorized cause](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Causa 2

Algumas possibilidades incluem:

- O dispositivo perdeu a conectividade de rede subjacente por mais tempo do que o [MQTT manter-se vivo,](iot-hub-mqtt-support.md#default-keep-alive-timeout)resultando em um tempo de tempo ocioso remoto. A configuração de manter-se vivo do MQTT pode ser diferente por dispositivo.

- O dispositivo enviou um reset tcp/ip-level, mas `MQTT DISCONNECT`não enviou um nível de aplicativo . Basicamente, o dispositivo fechou abruptamente a conexão subjacente do soquete. Às vezes, esse problema é causado por bugs em versões mais antigas do Azure IoT SDK.

- O aplicativo lateral do dispositivo caiu.

### <a name="cause-3"></a>Causa 3

IoT Hub pode estar passando por um problema transitório. Consulte [a causa de erro do servidor interno do IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Soluções

### <a name="solution-1"></a>Solução 1

Ver [401003 IoTHubUnauthorized solução 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Solução 2

- Certifique-se de que o dispositivo tenha boa conectividade com o IoT Hub [testando a conexão](tutorial-connectivity.md). Se a rede não for confiável ou intermitente, não recomendamos aumentar o valor de manter-se vivo porque pode resultar em detecção (via alertas do Monitor Do Azure, por exemplo) demorando mais. 

- Use as versões mais recentes dos [SDKs IoT](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Solução 3

Veja [soluções para erros internos do servidor do IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Próximas etapas

Recomendamos o uso de SDKs de dispositivos IoT do Azure para gerenciar conexões de forma confiável. Para saber mais, consulte [Gerenciar conectividade e mensagens confiáveis usando SDKs do dispositivo Azure IoT Hub](iot-hub-reliability-features-in-sdks.md)