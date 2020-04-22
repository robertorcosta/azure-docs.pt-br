---
title: Entenda o suporte ao MQTT do Serviço de Provisionamento de Dispositivos Azure IoT | Microsoft Docs
description: Guia do desenvolvedor - suporte para dispositivos conectados ao ponto final de dps (Azure IoT Device Provisioning Service, serviço de provisionamento de dispositivos Det) voltado para dispositivos usando o protocolo MQTT.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 213fc3412a2dfad77946e52a355a30774d6860c7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680684"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Comunique-se com seu DPS usando o protocolo MQTT

O DPS permite que os dispositivos se comuniquem com o ponto final do dispositivo DPS usando:

* [MQTT v3.1.1](https://mqtt.org/) na porta 8883
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) sobre WebSocket na porta 443.

O DPS não é um corretor MQTT completo e não suporta todos os comportamentos especificados no padrão MQTT v3.1.1. Este artigo descreve como os dispositivos podem usar comportamentos MQTT suportados para se comunicar com O DPS.

Toda a comunicação do dispositivo com DPS deve ser garantida usando TLS/SSL. Portanto, o DPS não suporta conexões não seguras sobre a porta 1883.

 > [!NOTE] 
 > O DPS não suporta atualmente dispositivos usando [o mecanismo de atestação](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) TPM sobre o protocolo MQTT.

## <a name="connecting-to-dps"></a>Conectando-se ao DPS

Um dispositivo pode usar o protocolo MQTT para se conectar a um DPS usando qualquer uma das seguintes opções.

* Bibliotecas nos [SDKs de provisionamento de IoT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks).
* O protocolo MQTT diretamente.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Usando o protocolo MQTT diretamente (como um dispositivo)

Se um dispositivo não puder usar os SDKs do dispositivo, ele poderá se conectar com os pontos de extremidade públicos do dispositivo usando o protocolo MQTT na porta 8883. No pacote **CONNECT,** o dispositivo deve usar os seguintes valores:

* Para o campo **ClientId,** use **registrationId**.

* Para o campo Nome `{idScope}/registrations/{registration_id}/api-version=2019-03-31`de `{idScope}` **Usuário,** use , onde está o [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) do DPS.

* No campo **Senha** use um token SAS. O formato do token SAS é o mesmo, conforme descrito para os protocolos HTTPS e AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`O recursoURI deve estar `{idScope}/registrations/{registration_id}`no formato . O nome da `registration`apólice deveria ser.

  > [!NOTE]
  > Se você usa a autenticação de certificado X.509, as senhas de token SAS não são necessárias.

  Para obter mais informações sobre como gerar tokens SAS, consulte a seção de tokens de segurança do [acesso control ao DPS](how-to-control-access.md#security-tokens).

A seguir está uma lista de comportamentos específicos da implementação do DPS:

 * O DPS não suporta a funcionalidade do sinalizador **CleanSession** que está sendo definido como **0**.

 * Quando um aplicativo de dispositivo assina um tópico com **QoS 2,** o DPS concede o nível 1 máximo de QoS no pacote **SUBACK.** Depois disso, o DPS entrega mensagens para o dispositivo usando o QoS 1.

## <a name="tlsssl-configuration"></a>Configuração de TLS/SSL

Para usar diretamente o protocolo MQTT, seu cliente *deve* se conectar pelo TLS 1.2. Tentativas de ignorar essa etapa falham com erros de conexão.


## <a name="registering-a-device"></a>Registrando um dispositivo

Para registrar um dispositivo através do DPS, um dispositivo deve se inscrever usando `$dps/registrations/res/#` como filtro de **tópico**. O curinga de vários níveis `#` no filtro de tópico é usado apenas para permitir que o dispositivo receba propriedades adicionais no nome do tópico. O DPS não permite `#` o `?` uso de curingas ou curingas para filtragem de subtópicos. Como o DPS não é um intermediário de mensagens pub-sub de uso geral, ele só suporta os nomes de tópicos documentados e filtros de tópicos.

O dispositivo deve publicar uma mensagem de registro no DPS usando `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` como nome de **tópico**. A carga deve conter o objeto [Registro do Dispositivo](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) no formato JSON.
Em um cenário bem-sucedido, o `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` dispositivo receberá uma resposta sobre o nome do tópico onde x é o valor de reavaliação em segundos. A carga da resposta conterá o objeto [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) no formato JSON.

## <a name="polling-for-registration-operation-status"></a>Sondagem para status de operação de registro

O dispositivo deve fazer uma pesquisa periódica no serviço para receber o resultado da operação de registro do dispositivo. Supondo que o dispositivo `$dps/registrations/res/#` já tenha subscrito o tópico como indicado acima, ele pode publicar uma mensagem de status de operação get para o nome do `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` tópico. O ID de operação nesta mensagem deve ser o valor recebido na mensagem de resposta RegistrationOperationStatus na etapa anterior. No caso bem-sucedido, o `$dps/registrations/res/200/?$rid={request_id}` serviço responderá sobre o tema. A carga da resposta conterá o objeto RegistrationOperationStatus. O dispositivo deve continuar pesquisando o serviço se o código de resposta for 202 após um atraso igual ao período de repetição. A operação de registro do dispositivo é bem sucedida se o serviço retornar um código de status de 200.

## <a name="connecting-over-websocket"></a>Conectando-se sobre websocket
Ao conectar-se pelo Websocket, `mqtt`especifique o subprotocolo como . Siga [a RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o protocolo MQTT, consulte a [documentação do MQTT](https://mqtt.org/documentation).

Para explorar melhor as capacidades do DPS, consulte:

* [Sobre o IoT DPS](about-iot-dps.md)
