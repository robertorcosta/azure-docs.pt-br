---
title: Solução de problemas do Hub IoT do Azure erro 401003 IoTHubUnauthorized
description: Entenda como corrigir o erro 401003 IoTHubUnauthorized
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759586"
---
# <a name="401003-iothubunauthorized"></a>IoTHubUnauthorized 401003

Este artigo descreve as causas e soluções para erros de **401003 IoTHubUnauthorized** .

## <a name="symptoms"></a>Sintomas

### <a name="symptom-1"></a>Sintoma 1

Nos logs de diagnóstico, você verá um padrão de dispositivos desconectando com **401003 IoTHubUnauthorized**, seguido por **404104 DeviceConnectionClosedRemotely**e, em seguida, se conectar com êxito logo após.

### <a name="symptom-2"></a>Sintoma 2

As solicitações ao Hub IoT falham com uma das seguintes mensagens de erro:

* Cabeçalho de autorização ausente
* IotHub '\*' não contém o dispositivo especificado '\*'
* A regra de\*autorização ' ' não permite acesso para\*' '
* Falha de autenticação para este dispositivo, renovar token ou certificado e reconectar
* A impressão digital não corresponde à configuração: impressão digital\*: SHA1Hash =\*, SHA2Hash =; Configuração: PrimaryThumbprint =\*, SecondaryThumbprint =\*

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

Para MQTT, alguns SDKs dependem do Hub IoT para emitir a desconexão quando o token SAS expirar para saber quando atualizá-lo. Portanto, 

1. O token SAS expira
1. O Hub IoT observa a expiração e desconecta o dispositivo com **401003 IoTHubUnauthorized**
1. O dispositivo conclui a desconexão com **404104 DeviceConnectionClosedRemotely**
1. O SDK do IoT gera um novo token SAS
1. O dispositivo se reconecta com o Hub IoT com êxito

### <a name="cause-2"></a>Causa 2

O Hub IoT não pôde autenticar o cabeçalho de autenticação, a regra ou a chave.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Nenhuma ação necessária se estiver usando o SDK do IoT para conexão usando a cadeia de conexão do dispositivo. O SDK do IoT regenera o novo token para se reconectar na expiração do token SAS. 

Se o volume de erros for uma preocupação, mude para o SDK do C, que renova o token SAS antes da expiração. Além disso, para AMQP, o token SAS pode ser atualizado sem a desconexão.

### <a name="solution-2"></a>Solução 2

Em geral, a mensagem de erro apresentada deve explicar como corrigir o erro. Se, por alguma razão, você não tiver acesso aos detalhes da mensagem de erro, verifique se:

- A SAS ou outro token de segurança que você usa não expirou. 
- A credencial de autorização é bem formada para o protocolo que você usa. Para saber mais, confira [controle de acesso do Hub IOT](iot-hub-devguide-security.md).
- A regra de autorização usada tem a permissão para a operação solicitada.

## <a name="next-steps"></a>Próximas etapas

Para facilitar a autenticação no Hub IoT, é recomendável usar [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).