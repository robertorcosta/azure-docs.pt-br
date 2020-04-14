---
title: Entender o suporte do MQTT do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ suporte para dispositivos que se conectam a um ponto de extremidade do Hub IoT voltado para o dispositivo usando o protocolo MQTT. Inclui informações sobre suporte interno do MQTT nos SDKs do dispositivo IoT do Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 9ccfaa57b8e8fdea325bed908ffe8815b09d0d15
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257786"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunicar com o hub IoT usando o protocolo MQTT

O Hub IoT permite que os dispositivos comuniquem-se com os pontos de extremidade do dispositivo de Hub IoT usando:

* [MQTT v3.1.1](https://mqtt.org/) na porta 8883
* MQTT v3.1.1 sobre WebSocket na porta 443.

O Hub IoT não é um agente MQTT completo e não dá suporte a todos os comportamentos especificados no padrão MQTT v3.1.1. Este artigo descreve como dispositivos podem usar comportamentos com suporte do MQTT para comunicar-se com o Hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Todas as comunicações de dispositivo com o Hub IoT devem ser protegidas usando TLS/SSL. Portanto, o IoT Hub não suporta conexões não seguras sobre a porta 1883.

## <a name="connecting-to-iot-hub"></a>Conectando-se ao Hub IoT

Um dispositivo pode usar o protocolo MQTT para se conectar a um hub IoT usando qualquer uma das seguintes opções.

* Bibliotecas nos [SDKs IoT do Azure](https://github.com/Azure/azure-iot-sdks).
* O protocolo MQTT diretamente.

A porta MQTT (8883) está bloqueada em muitos ambientes de rede corporativa e educacional. Se você não pode abrir a porta 8883 em seu firewall, recomendamos o uso de MQTT sobre Soquetes da Web. O MQTT sobre Soquetes web se comunica pela porta 443, que quase sempre está aberta em ambientes de rede. Para saber como especificar os protocolos MQTT e MQTT sobre soquetes da Web ao usar os SDKs IoT do Azure, consulte [Usando o dispositivo SDKs](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Usando os SDKs de dispositivo

Os [SDKs do cliente do dispositivo](https://github.com/Azure/azure-iot-sdks) que são compatíveis com o protocolo MQTT estão disponíveis para Java, Node.js, C, C# e Python. Os SDKs do dispositivo usam a cadeia de conexão do Hub IoT padrão para estabelecer uma conexão com um Hub IoT. Para usar o protocolo MQTT, o parâmetro do protocolo do cliente deve ser definido como **MQTT**. Você também pode especificar MQTT sobre Soquetes da Web no parâmetro de protocolo cliente. Por padrão, os SDKs do dispositivo se conectam a um Hub IoT com o sinalizador **CleanSession** definido como **0** e usam **QoS 1** para troca de mensagens com o Hub IoT.

Quando um dispositivo está conectado a um Hub IoT, os SDKs do dispositivo fornecem métodos que permitem que o dispositivo troque mensagens com um Hub IoT.

A tabela a seguir contém links para amostras de código para cada idioma suportado e especifica o parâmetro a ser usado para estabelecer uma conexão com o IoT Hub usando o protocolo MQTT ou MQTT sobre Soquetes da Web.

| Linguagem | Parâmetro do protocolo MQTT | MQTT sobre o parâmetro do protocolo de Soquetes da Web
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-dispositivo-mqtt. Mqtt | azure-iot-dispositivo-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable). MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [Tipo de transporte](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet). Mqtt | TransportType.Mqtt volta ao MQTT sobre soquetes da Web se o MQTT falhar. Para especificar o MQTT apenas em Soquetes da Web, use TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Suporta MQTT por padrão | Adicione `websockets=True` a chamada para criar o cliente |

O fragmento a seguir mostra como especificar o protocolo MQTT sobre Soquetes da Web ao usar o Azure IoT Node.js SDK:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

O fragmento a seguir mostra como especificar o protocolo MQTT sobre Soquetes da Web ao usar o Azure IoT Python SDK:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Tempo de tempo padrão manter-se vivo

Para garantir que uma conexão cliente/IoT Hub permaneça viva, tanto o serviço quanto o cliente enviam regularmente um ping *de manter-se vivo* um para o outro. O cliente que usa IoT SDK envia um keep-alive no intervalo definido nesta tabela abaixo:

|Linguagem  |Intervalo padrão de manter-se vivo  |Configurável  |
|---------|---------|---------|
|Node.js     |   180 segundos      |     Não    |
|Java     |    230 segundos     |     Não    |
|C     | 240 segundos |  [Sim](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 segundos |  [Sim](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python (V2)   | 60 segundos |  Não   |

Seguindo [a especificação MQTT,](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)o intervalo de ping keep-alive do IoT Hub é de 1,5 vezes o valor de manter vivo o cliente. No entanto, o IoT Hub limita o tempo limite máximo do lado do servidor para 29,45 minutos (1767 segundos) porque todos os serviços do Azure estão vinculados ao tempo limite de marcha do Azure, que é de 29,45 minutos. 

Por exemplo, um dispositivo que usa o Java SDK envia o ping keep-alive e perde a conectividade da rede. 230 segundos depois, o dispositivo perde o ping keep-alive porque está offline. No entanto, o IoT Hub não fecha a `(230 * 1.5) - 230 = 115` conexão imediatamente - ele espera mais alguns segundos antes de desconectar o dispositivo com o erro [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

O valor máximo de manter-se `1767 / 1.5 = 1177` vivo que você pode definir é segundos. Qualquer tráfego reiniciará o keep-alive. Por exemplo, uma atualização de token SAS bem-sucedida redefine o keep-alive.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrando um aplicativo de dispositivo de AMQP para MQTT

Se você estiver usando o [SDKs de cliente de dispositivo](https://github.com/Azure/azure-iot-sdks), a mudança do uso de AMQP para MQTT exige alteração do parâmetro de protocolo na inicialização do cliente, conforme mencionado anteriormente.

Ao fazer isso, verifique os seguintes itens:

* AMQP retorna erros para várias condições, enquanto MQTT encerra a conexão. Como resultado, sua lógica de manipulação de exceções pode exigir algumas alterações.

* MQTT não dá suporte a operações de *rejeição* quando recebe [mensagens da nuvem para do dispositivo](iot-hub-devguide-messaging.md). Se seu aplicativo de back-end precisar receber uma resposta do aplicativo do dispositivo, considere usar [métodos diretos](iot-hub-devguide-direct-methods.md).

* AmQP não é suportado no Python SDK

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Usando o protocolo MQTT diretamente (como um dispositivo)

Se um dispositivo não puder usar os SDKs do dispositivo, ele poderá se conectar com os pontos de extremidade públicos do dispositivo usando o protocolo MQTT na porta 8883. No pacote **CONNECT,** o dispositivo deve usar os seguintes valores:

* No campo **ClientId**, use o **deviceId**.

* Para o campo **Username**, use `{iothubhostname}/{device_id}/?api-version=2018-06-30`, onde `{iothubhostname}` é o CName completo do Hub IoT.

    Por exemplo, se o nome de seu Hub IoT for **contoso.azure-devices.net** e se o nome do dispositivo for **MyDevice01**, o campo **Username** completo deverá conter:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* No campo **Senha** use um token SAS. O formato do token SAS é o mesmo, conforme descrito para os protocolos HTTPS e AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Se você usa a autenticação de certificado X.509, as senhas de token SAS não são necessárias. Para obter mais informações, consulte [Configurar a segurança X.509 no seu Hub Azure IoT](iot-hub-security-x509-get-started.md) e siga as instruções de código [abaixo](#tlsssl-configuration).

  Para saber mais sobre como gerar tokens SAS, confira a seção de dispositivo de [Usar tokens de segurança do Hub IoT](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Ao testar, você também pode usar as ferramentas de IoT cross-platform [do Azure Para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou o hub de comando de extensão CLI [az iot generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token) para gerar rapidamente um token SAS que você pode copiar e colar em seu próprio código:

### <a name="for-azure-iot-tools"></a>Para ferramentas De IoT do Azure

1. Expanda a guia **AZURE IOT HUB DEVICES** no canto inferior esquerdo do Visual Studio Code.
  
2. Clique com o botão direito no dispositivo e selecione **Generate SAS Token for Device**.
  
3. Definir **tempo de expiração** e pressione 'Enter'.
  
4. O token SAS é criado e copiado para a área de transferência.

   O token de SAS gerado tem a seguinte estrutura:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   A parte desse token para usar como o campo **Senha** para conectar usando MQTT é:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Para que o MQTT conecte e desconecte pacotes, o Hub IoT emite um evento no canal **Monitoramento de Operações** . Este evento possui informações adicionais que podem ajudá-lo a solucionar problemas de conectividade.

O aplicativo de dispositivo pode especificar uma mensagem **Will** no pacote **CONNECT**. O aplicativo do dispositivo devem usar `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}`como o nome do tópico **Will** para definir mensagens **Will** a serem encaminhadas como uma mensagem de telemetria. Nesse caso, se a conexão de rede for fechada, mas um pacote **DISCONNECT** não tiver sido recebido anteriormente do dispositivo, o Hub IoT enviará a mensagem **Will** fornecida no pacote **CONNECT** para o canal de telemetria. O canal de telemetria pode ser o ponto de extremidade padrão **Eventos**, ou um ponto de extremidade personalizado definido pelo roteamento do Hub IoT. A mensagem tem a propriedade **iothub-MessageType** com um valor de **Will** atribuído a ele.

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>Um exemplo de código C usando MQTT sem Azure IoT C SDK
Neste [repositório,](https://github.com/Azure-Samples/IoTMQTTSample)você encontrará alguns projetos de demonstração C/C++ mostrando como enviar mensagens de telemetria, receber eventos com um hub de IoT sem usar o Azure IoT C SDK. 

Essas amostras usam a biblioteca Eclipse Mosquitto para enviar mensagem ao MQTT Broker implementado no hub IoT.

Este repositório contém:

**Para windows:**

* TelemetriaMQTTWin32: contém código para enviar uma mensagem de telemetria a um hub Azure IoT, construído e executado em uma máquina Windows.

* SubscribeMQTTWin32: contém código para assinar eventos de um determinado hub de IoT em uma máquina Windows.

* DeviceTwinMQTTWin32: contém código para consulta e subscreve os eventos gêmeos do dispositivo no hub Azure IoT em uma máquina Windows.

* PnPMQTTWin32: contém código para enviar uma mensagem de telemetria com ioT Plug & Reproduzir recursos de visualização do dispositivo para um hub Azure IoT, construído e executado em uma máquina Windows. Mais sobre IoT Plug & Play [aqui](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**Para Linux:**

* MQTTLinux: contém código e script de compilação para executar no Linux (WSL, Ubuntu e Raspbian foram testados até agora).

* LinuxConsoleVS2019: contém o mesmo código, mas em um projeto VS2019 direcionado ao WSL (subsistema Windows Linux). Este projeto permite que você depurao o código rodando no Linux passo a passo do Visual Studio.

**Para mosquitto_pub:**

Esta pasta contém dois comandos de amostras usados com mosquitto_pub ferramenta de utilidade fornecida por Mosquitto.org.

* Mosquitto_sendmessage: enviar uma mensagem de texto simples para um hub Azure IoT agindo como um dispositivo.

* Mosquitto_subscribe: ver eventos ocorrendo em um hub DeI azure.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Usando o protocolo MQTT diretamente (como um módulo)

Conectar ao Hub IoT por meio de MQTT usando uma identidade de módulo é semelhante ao dispositivo (descrito [acima](#using-the-mqtt-protocol-directly-as-a-device)), mas você precisa usar o seguinte:

* Defina o ID do cliente para `{device_id}/{module_id}`.

* Se autenticando com o nome de usuário e senha, defina o nome de usuário como `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` e use o token SAS associado à identidade de módulo como sua senha.

* Use `devices/{device_id}/modules/{module_id}/messages/events/` como tópico para a publicação de telemetria.

* Use `devices/{device_id}/modules/{module_id}/messages/events/` como o tópico WILL.

* Os tópicos gêmeos GET e PATCH são idênticos para módulos e dispositivos.

* Os tópicos de status gêmeos são idênticos para módulos e dispositivos.

## <a name="tlsssl-configuration"></a>Configuração de TLS/SSL

Para usar o protocolo MQTT diretamente, o cliente *deve* se conectar por TLS/SSL. Tentativas de ignorar essa etapa falham com erros de conexão.

Para estabelecer uma conexão TLS, você precisará baixar e referenciar o certificado de raiz DigiCert Baltimore. Esse certificado é utilizado pelo Azure para proteger a conexão. É possível localizar esse certificado no repositório [Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c). Para obter mais informações sobre esses certificados, acesse o [site da Digicert](https://www.digicert.com/digicert-root-certificates.htm).

Um exemplo de como implementar isso usando a versão do Python da [biblioteca Paho MQTT](https://pypi.python.org/pypi/paho-mqtt) pelo Eclipse Foundation se parece com o seguinte.

Primeiro, instale a biblioteca Paho do seu ambiente de linha de comando:

```cmd/sh
pip install paho-mqtt
```

Em seguida, implemente o cliente em um script Python. Substitua os espaços reservados conforme a seguir:

* `<local path to digicert.cer>` é o caminho para um arquivo local que contém o certificado DigiCert Baltimore Root. Você pode criar esse arquivo, copiando as informações sobre o certificado do [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) no SDK de IoT do Azure para C. Inclua as linhas `-----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----`, remova as marcas `"` no início e no final de cada linha, e remova os caracteres `\r\n` no final de cada linha.

* `<device id from device registry>` é a ID de um dispositivo que você adicionou ao seu Hub IoT.

* `<generated SAS token>` é um token de SAS para o dispositivo criado, conforme descrito anteriormente neste artigo.

* `<iot hub name>` o nome do seu Hub IoT.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Para autenticar usando um certificado de dispositivo, atualize o trecho de código acima com as seguintes alterações (veja [Como obter um certificado de CA X.509](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) sobre como se preparar para autenticação baseada em certificados):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Enviando mensagens de dispositivo para nuvem

Depois de fazer uma conexão bem-sucedida, um dispositivo pode enviar mensagens ao IoT Hub usando `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` como um **nome de tópico**. O elemento `{property_bag}` habilita o dispositivo a enviar mensagens com propriedades adicionais em um formato codificado de URL. Por exemplo:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Este `{property_bag}` elemento usa a mesma codificação que as strings de consulta no protocolo HTTPS.

Veja a seguir uma lista dos comportamentos específicos à implementação do Hub IoT:

* O Hub IoT não dá suporte a mensagens de QoS 2. Se um aplicativo de dispositivo publicar uma mensagem com o **QoS 2**, o Hub IoT fechará a conexão de rede.

* O Hub IoT não persiste mensagens de retenção. Se um dispositivo envia uma mensagem com o sinalizador **RETAIN** definido como 1, o Hub IoT adiciona a propriedade de aplicativo **x-opt-retain** à mensagem. Nesse caso, em vez de persistir a mensagem de retenção, o Hub IoT a transmite ao aplicativo de back-end.

* O Hub IoT oferece suporte apenas a uma conexão MQTT ativa por dispositivo. Qualquer nova conexão MQTT em nome da mesma ID de dispositivo faz com que o Hub IoT descarte a conexão existente.

Para obter mais informações, consulte [Guia do desenvolvedor do sistema de mensagens](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Recebendo mensagens da nuvem para o dispositivo

Para receber mensagens do IoT Hub, `devices/{device_id}/messages/devicebound/#` um dispositivo deve se inscrever usando como filtro de **tópico**. O curinga de vários níveis `#` no filtro de tópico é usado apenas para permitir que o dispositivo receba propriedades adicionais no nome do tópico. O Hub IoT não permite o uso dos caracteres curinga `#` ou `?` para filtragem de subtópicos. Como o Hub IoT não é um agente de mensagens pub-sub de uso geral, ele dá suporte somente para nomes de tópicos e filtros tópicos documentados.

O dispositivo só receberá mensagens do Hub IoT depois de assinar com êxito o ponto de extremidade específico ao dispositivo, representado pelo filtro de tópico `devices/{device_id}/messages/devicebound/#`. Depois que uma assinatura é estabelecida, o dispositivo receberá mensagens de nuvem para dispositivo que foram enviadas após o horário da assinatura. Se o dispositivo se conectar com o sinalizador **CleanSession** definido como **0**, a assinatura será persistida entre as diferentes sessões. Neste caso, a próxima vez que o dispositivo conectar-se com **CleanSession 0**, ele receberá todas as mensagens pendentes enviadas a ele enquanto estava desconectado. Se o dispositivo usar o sinalizador **CleanSession** definido como **1**, não receberá todas as mensagens do Hub IoT até que ele se inscreva no ponto de extremidade do dispositivo.

O Hub IoT entrega mensagens com o **Nome do Tópico** `devices/{device_id}/messages/devicebound/` ou `devices/{device_id}/messages/devicebound/{property_bag}` quando há propriedades de mensagens. `{property_bag}` contém pares de chave/valor codificados de URL das propriedades da mensagem. Somente propriedades de aplicativo e propriedades do sistema definível pelo usuário (como **messageId** ou **correlationId**) estão incluídas no recipiente de propriedades. Os nomes dos **$** imóveis do sistema têm o prefixo, as propriedades do aplicativo usam o nome original da propriedade sem prefixo.

Quando um aplicativo do dispositivo assina um tópico com o **QoS 2**, o Hub IoT concede, no máximo, o nível 1 do QoS no pacote **SUBACK**. Depois disso, o Hub IoT entrega mensagens ao dispositivo usando QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Recuperando as propriedades de um dispositivo gêmeo

Primeiro, um dispositivo assina `$iothub/twin/res/#` para receber respostas da operação. Em seguida, ele envia uma mensagem vazia ao tópico `$iothub/twin/GET/?$rid={request id}`, com um valor preenchido como a **ID da solicitação**. O serviço então envia uma mensagem de resposta que contém os dados do dispositivo gêmeo no tópico `$iothub/twin/res/{status}/?$rid={request id}`, usando a mesma **ID de solicitação** da solicitação.

A ID da solicitação pode ser qualquer valor válido de um valor da propriedade de mensagem, de acordo com o [Guia do desenvolvedor do sistema de mensagens do Hub IoT](iot-hub-devguide-messaging.md) e o status é validado como um inteiro.

O corpo de resposta contém a seção de propriedades do dispositivo gêmeo, como no seguinte exemplo de resposta:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Os códigos de status possíveis são:

|Status | Descrição |
| ----- | ----------- |
| 200 | Sucesso |
| 429 | Número excessivo de solicitações (limitado), de acordo com a [Limitação do Hub IoT](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros do servidor |

Para obter mais informações, consulte [Guia do desenvolvedor de dispositivos gêmeos](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Atualizar as propriedades relatadas do dispositivo gêmeo

Para atualizar as propriedades relatadas, o dispositivo emite uma solicitação para o Hub IoT por meio de uma publicação ao longo de um tópico MQTT designado. Depois de processar a solicitação, o Hub IoT responde o status de êxito ou falha da operação de atualização por meio de uma publicação para outro tópico. Este tópico pode ser assinado pelo dispositivo para notificá-lo sobre o resultado de sua solicitação de atualização de gêmeos. Para implementar esse tipo de interação solicitação/resposta no MQTT,`$rid`aproveitamos a noção de ID de solicitação ( ) fornecida inicialmente pelo dispositivo em sua solicitação de atualização. Este ID de solicitação também está incluído na resposta do IoT Hub para permitir que o dispositivo correlaciona a resposta à sua solicitação anterior particular.

A sequência a seguir descreve como um dispositivo atualiza as propriedades relatadas no dispositivo gêmeo no Hub IoT:

1. Primeiro, um dispositivo deve assinar o tópico `$iothub/twin/res/#` para receber respostas da operação do Hub IoT.

2. Um dispositivo envia uma mensagem que contém a atualização do dispositivo gêmeo para o tópico `$iothub/twin/PATCH/properties/reported/?$rid={request id}`. Essa mensagem inclui um valor de **id da solicitação**.

3. Em seguida, o serviço envia uma mensagem de resposta que contém o novo valor de ETag para a coleção de propriedades relatadas no tópico `$iothub/twin/res/{status}/?$rid={request id}`. Essa mensagem de resposta usa a mesma **id de solicitação** da solicitação.

O corpo da mensagem de solicitação contém um documento JSON, que contém novos valores para propriedades relatadas. Cada membro do documento JSON atualiza ou adiciona o membro correspondente no documento do dispositivo gêmeo. Um membro definido como `null` exclui o membro do objeto recipiente. Por exemplo:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Os códigos de status possíveis são:

|Status | Descrição |
| ----- | ----------- |
| 204 | Êxito (nenhum conteúdo retorna) |
| 400 | Solicitação inválida. JSON malformado |
| 429 | Número excessivo de solicitações (limitado), de acordo com a [Limitação do Hub IoT](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros do servidor |

O snippet de código python abaixo, demonstra o processo de atualização de propriedades relatadas de gêmeos sobre MQTT (usando o cliente Paho MQTT):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Após o êxito do gêmeo relatado na operação de atualização de propriedades acima, a mensagem de publicação do Hub IoT terá o seguinte tópico: `$iothub/twin/res/204/?$rid=1&$version=6`, onde `204` é o código de status indicando sucesso, `$rid=1` corresponde à ID da solicitação fornecido pelo dispositivo no código, e `$version` corresponde à versão da seção de propriedades relatadas de gêmeos de dispositivo após a atualização.

Para obter mais informações, consulte [Guia do desenvolvedor de dispositivos gêmeos](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Recebendo notificações de atualização de propriedades desejadas

Quando um dispositivo é conectado, o Hub IoT envia notificações para o tópico `$iothub/twin/PATCH/properties/desired/?$version={new version}`, que contêm o conteúdo da atualização executada pelo back-end da solução. Por exemplo:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Quanto às atualizações `null` de propriedades, os valores significam que o membro do objeto JSON está sendo excluído. Além disso, observe que `$version` indica a nova versão da seção de propriedades desejada do dispositivo gêmeo.

> [!IMPORTANT]
> O Hub IoT gera notificações de alteração somente quando os dispositivos estão conectados. Lembre-se de implementar o [fluxo de reconexão do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) para manter as propriedades desejadas sincronizadas entre o Hub IoT e o aplicativo do dispositivo.

Para obter mais informações, consulte [Guia do desenvolvedor de dispositivos gêmeos](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Responder a um método direto

Primeiro, um dispositivo precisa assinar `$iothub/methods/POST/#`. O Hub IoT envia solicitações de método para o tópico `$iothub/methods/POST/{method name}/?$rid={request id}` com um corpo vazio ou JSON válido.

Para responder, o dispositivo envia uma mensagem com um JSON válido ou um corpo vazio para o tópico `$iothub/methods/res/{status}/?$rid={request id}`. Nessa mensagem, a **ID da Solicitação** deve corresponder com o da mensagem de solicitação e o **status** deve ser um número inteiro.

Para obter mais informações, consulte [Guia do desenvolvedor do método direto](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Considerações adicionais

Como uma consideração final, se for necessário personalizar o comportamento do protocolo MQTT na nuvem, você deverá revisar o [gateway de protocolo de IoT do Azure ](iot-hub-protocol-gateway.md). Esse software permite que você implante um gateway de protocolo personalizado de alto desempenho que faz interface diretamente com o Hub IoT. O gateway do protocolo IoT do Azure permite que você personalize o protocolo de dispositivo para acomodar as implantações de MQTT de nível industrial ou outros protocolos personalizados. Essa abordagem exige, no entanto, que você execute e opere um gateway de protocolo personalizado.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o protocolo MQTT, consulte a [documentação do MQTT](https://mqtt.org/documentation).

Para saber mais sobre como planejar sua implantação do Hub IoT, consulte:

* [Catálogo de dispositivos Azure Certified para IoT](https://catalog.azureiotsolutions.com/)
* [Suporte a protocolos adicionais](iot-hub-protocol-gateway.md)
* [Comparar com Hubs de Eventos](iot-hub-compare-event-hubs.md)
* [Escala, alta disponibilidade e recuperação de desastre](iot-hub-scaling.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia de desenvolvedores do IoT Hub](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
