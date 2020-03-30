---
title: Entenda o suporte amqp do Azure IoT Hub | Microsoft Docs
description: Guia do desenvolvedor - suporte para dispositivos conectados a pontos finais voltados para dispositivos IoT Hub e voltados para serviços usando o Protocolo AMQP. Inclui informações sobre suporte AMQP incorporado nos SDKs do dispositivo Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284779"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Comunique-se com seu hub de IoT usando o Protocolo AMQP

O Azure IoT Hub oferece suporte ao [OASIS Advanced Message Queuing Protocol (AMQP) versão 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) para fornecer uma variedade de funcionalidades através de pontos finais voltados para dispositivos e de serviço. Este documento descreve o uso de clientes AMQP para se conectar a um hub de IoT para usar a funcionalidade do IoT Hub.

## <a name="service-client"></a>Cliente de serviço

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Conecte-se e autentique a um hub de IoT (cliente de serviço)

Para se conectar a um hub de IoT usando AMQP, um cliente pode usar a autenticação de [segurança baseada em sinistros (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ou [Simples Autenticação e Camada de Segurança (SASL).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

As seguintes informações são necessárias para o cliente do serviço:

| Informações | Valor |
|-------------|--------------|
| Nome de host do hub ioT | `<iot-hub-name>.azure-devices.net` |
| Nome da chave | `service` |
| Chave de acesso | Uma chave primária ou secundária que está associada ao serviço |
| Assinatura de acesso compartilhado | Uma assinatura de acesso compartilhado de curta `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`duração no seguinte formato: . Para obter o código para gerar essa assinatura, consulte [Controle de acesso ao IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

O trecho de código a seguir usa a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python) para se conectar a um hub IoT através de um link de remetente.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Invoque mensagens nuvem-para-dispositivo (cliente de serviço)

Para saber mais sobre a troca de mensagens nuvem-dispositivo entre o serviço e o hub ioT e entre o dispositivo e o hub IoT, consulte [Enviar mensagens nuvem-para-dispositivo a partir do seu hub IoT](iot-hub-devguide-messages-c2d.md). O cliente do serviço usa dois links para enviar mensagens e receber feedback para mensagens enviadas anteriormente de dispositivos, conforme descrito na tabela a seguir:

| Criado por | Tipo de vínculo | Caminho de link | Descrição |
|------------|-----------|-----------|-------------|
| Serviço | Link de remetente | `/messages/devicebound` | Mensagens de nuvem para dispositivo destinadas a dispositivos são enviadas para este link pelo serviço. As mensagens enviadas por `To` este link têm sua propriedade definida `/devices/<deviceID>/messages/devicebound`no caminho de link receptor do dispositivo de destino, . |
| Serviço | Link do receptor | `/messages/serviceBound/feedback` | Conclusão, rejeição e mensagens de feedback de abandono que vêm de dispositivos recebidos neste link por serviço. Para obter mais informações sobre mensagens de feedback, consulte [Enviar mensagens em nuvem para dispositivo de um hub de IoT](./iot-hub-devguide-messages-c2d.md#message-feedback). |

O trecho de código a seguir demonstra como criar uma mensagem nuvem-para-dispositivo e enviá-la para um dispositivo usando a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Para receber feedback, o cliente do serviço cria um link receptor. O trecho de código a seguir demonstra como criar um link usando a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Como mostrado no código anterior, uma mensagem de feedback nuvem-para-dispositivo tem um tipo de conteúdo de *aplicativo/vnd.microsoft.iothub.feedback.json*. Você pode usar as propriedades no corpo JSON da mensagem para inferir o status de entrega da mensagem original:

* A `statusCode` chave no corpo de feedback tem um dos seguintes valores: *Sucesso,* *Expirado,* *EntregaContagem ultrapassada,* *rejeitada*ou *purgada.*

* A `deviceId` chave no corpo de feedback tem o ID do dispositivo de destino.

* A `originalMessageId` chave no corpo de feedback tem o ID da mensagem original nuvem-para-dispositivo que foi enviada pelo serviço. Você pode usar esse status de entrega para correlacionar feedback com mensagens de nuvem para dispositivo.

### <a name="receive-telemetry-messages-service-client"></a>Receba mensagens de telemetria (cliente de serviço)

Por padrão, seu hub de IoT armazena mensagens de telemetria de dispositivos ingeridas em um hub de eventos incorporado. Seu cliente de serviço pode usar o Protocolo AMQP para receber os eventos armazenados.

Para isso, o cliente de serviço precisa primeiro se conectar ao ponto final do hub de IoT e receber um endereço de redirecionamento para os hubs de eventos incorporados. Em seguida, o cliente do serviço usa o endereço fornecido para se conectar ao hub de eventos incorporado.

Em cada etapa, o cliente precisa apresentar as seguintes informações:

* Credenciais de serviço válidas (token de assinatura de acesso compartilhado de serviço).

* Um caminho bem formatado para a partição do grupo de consumidores da a que pretende recuperar mensagens. Para um determinado grupo de consumidores e ID `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` de partição, `$Default`o caminho tem o seguinte formato: (o grupo de consumidores padrão é ).

* Um predicado de filtragem opcional para designar um ponto de partida na partição. Este predicado pode ser na forma de um número de seqüência, deslocamento ou carimbo de tempo enfileirado.

O seguinte trecho de código usa a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python) para demonstrar as etapas anteriores:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Para um determinado ID do dispositivo, o hub IoT usa um hash do ID do dispositivo para determinar qual partição armazenar suas mensagens. O trecho de código anterior demonstra como os eventos são recebidos de uma única partição desse tipo. No entanto, observe que um aplicativo típico muitas vezes precisa recuperar eventos armazenados em todas as partições do hub de eventos.

## <a name="device-client"></a>Cliente do dispositivo

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Conecte-se e autentique a um hub ioT (cliente do dispositivo)

Para se conectar a um hub de IoT usando AMQP, um dispositivo pode usar a autenticação [de Segurança Baseada em Sinistros (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ou simples [autenticação e camada de segurança (SASL).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

As seguintes informações são necessárias para o cliente do dispositivo:

| Informações | Valor |
|-------------|--------------|
| Nome de host do hub ioT | `<iot-hub-name>.azure-devices.net` |
| Chave de acesso | Uma chave primária ou secundária associada ao dispositivo |
| Assinatura de acesso compartilhado | Uma assinatura de acesso compartilhado de curta `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`duração no seguinte formato: . Para obter o código para gerar essa assinatura, consulte [Controle de acesso ao IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

O trecho de código a seguir usa a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python) para se conectar a um hub IoT através de um link de remetente.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Os seguintes caminhos de link são suportados como operações de dispositivos:

| Criado por | Tipo de vínculo | Caminho de link | Descrição |
|------------|-----------|-----------|-------------|
| Dispositivos | Link do receptor | `/devices/<deviceID>/messages/devicebound` | Mensagens de nuvem para dispositivo destinadas a dispositivos são recebidas neste link por cada dispositivo de destino. |
| Dispositivos | Link de remetente | `/devices/<deviceID>/messages/events` | Mensagens de dispositivo para nuvem enviadas a partir de um dispositivo são enviadas por este link. |
| Dispositivos | Link de remetente | `/messages/serviceBound/feedback` | Feedback de mensagem nuvem-dispositivo enviado ao serviço por este link por dispositivos. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Receba comandos nuvem-dispositivo (cliente do dispositivo)

Os comandos nuvem-para-dispositivo enviados aos `/devices/<deviceID>/messages/devicebound` dispositivos chegam em um link. Os dispositivos podem receber essas mensagens em lotes e usar a carga de dados de mensagem, propriedades de mensagens, anotações ou propriedades do aplicativo na mensagem conforme necessário.

O trecho de código a seguir usa a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python)) para receber mensagens nuvem-dispositivo por um dispositivo.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Enviar mensagens de telemetria (cliente do dispositivo)

Você também pode enviar mensagens de telemetria de um dispositivo usando AMQP. O dispositivo pode fornecer opcionalmente um dicionário de propriedades do aplicativo ou várias propriedades de mensagem, como id de mensagem.

O trecho de código a seguir usa a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python) para enviar mensagens dispositivo-nuvem de um dispositivo.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Observações adicionais

* As conexões AMQP podem ser interrompidas por causa de uma falha de rede ou da expiração do token de autenticação (gerado no código). O cliente do serviço deve lidar com essas circunstâncias e restabelecer a conexão e os links, se necessário. Se um token de autenticação expirar, o cliente pode evitar uma queda de conexão renovando proativamente o token antes de sua expiração.

* Seu cliente deve ocasionalmente ser capaz de lidar com redirecionamentos de link corretamente. Para entender tal operação, consulte a documentação do cliente AMQP.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Protocolo AMQP, consulte a [especificação AMQP v1.0](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Para saber mais sobre as mensagens do IoT Hub, consulte:

* [Mensagens de nuvem para dispositivo](./iot-hub-devguide-messages-c2d.md)
* [Suporte para protocolos adicionais](iot-hub-protocol-gateway.md)
* [Suporte ao Protocolo de Transporte de Telemetria de Fila de Mensagens (MQTT)](./iot-hub-mqtt-support.md)
