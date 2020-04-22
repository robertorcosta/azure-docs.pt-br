---
title: Solução de problemas Erro do Hub IoT do Azure 401003 IoTHubUnauthorized
description: Entenda como corrigir erro 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f46d41c8287d03cbe9582ed560244cbd85cdeeaa
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759586"
---
# <a name="401003-iothubunauthorized"></a>IoTHubUnauthorized 401003

Este artigo descreve as causas e soluções para erros **de IoTHubUnauthorized 401003.**

## <a name="symptoms"></a>Sintomas

### <a name="symptom-1"></a>Sintoma 1

Nos registros de diagnóstico, você vê um padrão de dispositivos desconectando-se com **o IoTHubUnauthorized 401003,** seguido por **404104 DeviceConnectionClosedRemotely**, e, em seguida, conectando-se com sucesso pouco depois.

### <a name="symptom-2"></a>Sintoma 2

As solicitações ao IoT Hub falham com uma das seguintes mensagens de erro:

* Cabeçalho de autorização ausente
* IotHub\*' ' não contém o\*dispositivo especificado ' '
* Regra de\*autorização ' '\*não permite acesso para '
* Falha na autenticação deste dispositivo, renovar token ou certificado e reconectar
* A impressão digital não corresponde à configuração:\*Impressão digital:\*SHA1Hash= , SHA2Hash= ; Configuração: PrimaryThumbprint=\*, SecondaryThumbprint=\*

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

Para o MQTT, alguns SDKs dependem do IoT Hub para emitir a desconexão quando o token SAS expirar para saber quando atualizá-lo. Portanto, 

1. O token SAS expira
1. O IoT Hub nota a expiração e desconecta o dispositivo com **o IoTHubUnauthorized 401003**
1. O dispositivo completa a desconexão com **404104 DeviceConnectionClosedRemotely**
1. O IoT SDK gera um novo token SAS
1. O dispositivo se reconecta com o IoT Hub com sucesso

### <a name="cause-2"></a>Causa 2

O IoT Hub não conseguiu autenticar o cabeçalho, regra ou chave do auth.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Nenhuma ação necessária se usar o IoT SDK para conexão usando a seqüência de conexão do dispositivo. O IoT SDK regenera o novo token para se reconectar na expiração do token SAS. 

Se o volume de erros for uma preocupação, mude para o C SDK, que renova o token SAS antes do vencimento. Além disso, para AMQP o token SAS pode atualizar sem desconexão.

### <a name="solution-2"></a>Solução 2

Em geral, a mensagem de erro apresentada deve explicar como corrigir o erro. Se, por algum motivo, você não tiver acesso ao detalhe da mensagem de erro, certifique-se:

- O SAS ou outro token de segurança que você usa não está expirado. 
- A credencial de autorização é bem formada para o protocolo que você usa. Para saber mais, consulte [o controle de acesso do IoT Hub](iot-hub-devguide-security.md).
- A regra de autorização utilizada tem a permissão para a operação solicitada.

## <a name="next-steps"></a>Próximas etapas

Para facilitar a autenticação do IoT Hub, recomendamos o uso [de SDKs IoT do Azure](iot-hub-devguide-sdks.md).