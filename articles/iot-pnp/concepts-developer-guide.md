---
title: Guia do desenvolvedor - IoT Plug and Play Preview | Microsoft Docs
description: Descrição da modelagem do dispositivo para desenvolvedores de IoT Plug and Play
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605213"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Guia de desenvolvedor de modelagem ioT plug and Play Preview

O IoT Plug and Play Preview permite que você crie dispositivos que anunciam suas capacidades em aplicativos Azure IoT. Os dispositivos IoT Plug and Play não exigem configuração manual quando um cliente os conecta a aplicativos habilitados para Plug e Reprodução ioT. IoT Central é um exemplo de um aplicativo ioT plug e play-enabled.

Para construir um dispositivo IoT Plug and Play, você precisa criar uma descrição do dispositivo. A descrição é feita com uma linguagem de definição simples chamada Digital Twins Definition Language (DTDL).

## <a name="device-capability-model"></a>Modelo de funcionalidade do dispositivo

Com o DTDL, você cria um _modelo de capacidade_ de dispositivo para descrever as partes do seu dispositivo. Um dispositivo IoT típico é composto de:

- Peças personalizadas, que são as coisas que tornam seu dispositivo único.
- Peças padrão, que são coisas comuns a todos os dispositivos.

Essas peças são chamadas _interfaces_ em um modelo de capacidade do dispositivo. As interfaces definem os detalhes de cada parte que seu dispositivo implementa.

O exemplo a seguir mostra o modelo de capacidade do dispositivo para um dispositivo termostato:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Um modelo de capacidade tem alguns campos necessários:

- `@id`: um ID único na forma de um simples nome de recurso uniforme.
- `@type`: declara que este objeto é um modelo de capacidade.
- `@context`: especifica a versão DTDL usada para o modelo de capacidade.
- `implements`: lista as interfaces que seu dispositivo implementa.

Cada entrada na lista de interfaces na seção implementos tem:

- `name`: o nome de programação da interface.
- `schema`: a interface que o modelo de capacidade implementa.

Existem campos opcionais adicionais que você pode usar para adicionar mais detalhes ao modelo de capacidade, como nome de exibição e descrição. Interfaces que são declaradas dentro de um modelo de capacidade podem ser consideradas como componentes do dispositivo. Para visualização pública, a lista de interfaces pode ter apenas uma entrada por esquema.

## <a name="interface"></a>Interface

Com o DTDL, você descreve os recursos do seu dispositivo usando interfaces. As interfaces descrevem as _propriedades,_ _a telemetria_e _comanda_ uma parte dos implementos do dispositivo:

- `Properties`. Propriedades são campos de dados que representam o estado do seu dispositivo. Use propriedades para representar o estado durável do dispositivo, como o estado on-off de uma bomba de refrigeração. As propriedades também podem representar propriedades básicas do dispositivo, como a versão de firmware do dispositivo. Você pode declarar as propriedades como somente leitura ou gravável.
- `Telemetry`. Os campos de telemetria representam medições de sensores. Sempre que o dispositivo faz uma medição do sensor, ele deve enviar um evento de telemetria contendo os dados do sensor.
- `Commands`. Os comandos representam métodos que os usuários do seu dispositivo podem executar no dispositivo. Por exemplo, um comando de reset ou um comando para ligar ou desligar um ventilador.

O exemplo a seguir mostra a interface de um dispositivo termostato:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Uma interface tem alguns campos necessários:

- `@id`: um ID único na forma de um simples nome de recurso uniforme.
- `@type`: declara que este objeto é uma interface.
- `@context`: especifica a versão DTDL usada para a interface.
- `contents`: lista as propriedades, a telemetria e os comandos que compõem o seu dispositivo.

Neste simples exemplo, há apenas um único campo de telemetria. Uma descrição de campo mínima tem:

- `@type`: especifica o tipo `Telemetry`de `Property`capacidade: , ou `Command`.
- `name`: fornece o nome do valor da telemetria.
- `schema`: especifica o tipo de dados para a telemetria. Este valor pode ser um tipo primitivo, como duplo, inteiro, booleano ou corda. Tipos de objetos complexos, matrizes e mapas também são suportados.

Outros campos opcionais, como nome de exibição e descrição, permitem adicionar mais detalhes à interface e aos recursos.

### <a name="properties"></a>Propriedades

Por padrão, as propriedades são somente leitura. Propriedades somente leitura significam que o dispositivo relata atualizações de valor de propriedade para o seu hub IoT. Seu hub de IoT não pode definir o valor de uma propriedade somente leitura.

Você também pode marcar uma propriedade como gravável em uma interface. Um dispositivo pode receber uma atualização para uma propriedade gravável do seu hub de IoT, bem como relatar atualizações de valor de propriedade para o seu hub.

Os dispositivos não devem ser conectados para definir valores de propriedade. Os valores atualizados são transferidos quando o dispositivo em seguida se conecta ao hub. Esse comportamento se aplica tanto a propriedades legíveis quanto graváveis.

Não use propriedades para enviar telemetria do seu dispositivo. Por exemplo, uma propriedade `temperatureSetting=80` somente leitura, como deve significar que a temperatura do dispositivo foi definida para 80, e o dispositivo está tentando chegar ou ficar nesta temperatura.

Para propriedades graváveis, o aplicativo do dispositivo retorna um código de status de estado desejado, versão e descrição para indicar se recebeu e aplicou o valor da propriedade.

### <a name="telemetry"></a>Telemetria

Por padrão, o IoT Hub encaminha todas as mensagens de telemetria dos dispositivos para o seu [ponto final integrado voltado para**serviços (mensagens/eventos)**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) compatível com [hubs de eventos](https://azure.microsoft.com/documentation/services/event-hubs/).

Você pode usar [os pontos finais personalizados do IoT Hub e as regras de roteamento](../iot-hub/iot-hub-devguide-messages-d2c.md) para enviar telemetria para outros destinos, como armazenamento blob ou outros hubs de eventos. Regras de roteamento usam propriedades de mensagem para selecionar mensagens.

### <a name="commands"></a>Comandos

Os comandos são síncronos ou assíncronos. Um comando síncrono deve ser executado dentro de 30 segundos por padrão, e o dispositivo deve ser conectado quando o comando chegar. Se o dispositivo responder a tempo ou o dispositivo não estiver conectado, o comando falhará.

Use comandos assíncronos para operações de longa duração. O dispositivo envia informações de progresso usando mensagens de telemetria. Essas mensagens de progresso têm as seguintes propriedades de cabeçalho:

- `iothub-command-name`: o nome do `UpdateFirmware`comando, por exemplo.
- `iothub-command-request-id`: o ID de solicitação gerado no lado do servidor e enviado para o dispositivo na chamada inicial.
- `iothub-interface-id`: O ID da interface em que `urn:example:AssetTracker:1`este comando é definido, por exemplo.
 `iothub-interface-name`: o nome de instância `myAssetTracker`desta interface, por exemplo .
- `iothub-command-statuscode`: o código de status retornado `202`do dispositivo, por exemplo.

## <a name="register-a-device"></a>Registrar um dispositivo

IoT Plug and Play facilita a publicidade das capacidades do seu dispositivo. Com ioT Plug and Play, depois que seu dispositivo se conectar ao IoT Hub, você deve registrar o modelo de capacidade do dispositivo. O registro permite que os clientes usem os recursos ioT plug and play do seu dispositivo.

Este guia mostra como registrar um dispositivo usando o Dispositivo Azure IoT SDK para C.

Para cada interface que seu dispositivo implementa, você deve criar uma interface e conectá-la à sua implementação.

Para a interface do termostato mostrada anteriormente, usando o C SDK, você cria e conecta sua interface de termostato à sua implementação:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Repita este código para cada interface que seu dispositivo implementa.

Depois de criar uma interface, registre seu modelo de capacidade do dispositivo e interfaces com seu hub de IoT:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Use um dispositivo

O IoT Plug and Play permite que você use dispositivos que registraram suas capacidades com seu hub ioT. Por exemplo, você pode acessar as propriedades e comandos de um dispositivo diretamente.

Para usar um dispositivo IoT Plug and Play conectado ao seu hub IoT, use a API IoT Hub REST ou um dos SDKs de linguagem IoT. Os exemplos a seguir usam a API IoT Hub REST. A versão atual da `2019-07-01-preview`API é . Adere `?api-version=2019-07-01-preview` às suas chamadas de REST PI.

Para obter o valor de uma propriedade de`fwVersion`dispositivo, `DeviceInformation` como a versão de firmware ( ) na interface no termostato, você usa a API de gêmeos digitais REST.

Se o dispositivo do termostato for chamado, `t-123`você obterá todas as propriedades em todas as interfaces implementadas pelo seu dispositivo com uma chamada REST API GET:

```REST
GET /digitalTwins/t-123/interfaces
```

De forma mais geral, todas as propriedades em todas as `{device-id}` interfaces são acessadas com este modelo de API REST onde está o identificador do dispositivo:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Se você sabe o nome da `deviceInformation`interface, como , e deseja obter propriedades para essa interface específica, escopo a solicitação para uma interface específica por nome:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

De forma mais geral, as propriedades para uma interface específica `device-id` podem ser acessadas através deste modelo de API REST onde está o identificador do dispositivo e `{interface-name}` é o nome da interface:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Você pode chamar os comandos do dispositivo IoT Plug and Play diretamente. Se `Thermostat` a interface `t-123` no `restart` dispositivo tiver um comando, você pode chamá-la com uma chamada REST API POST:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

De forma mais geral, os comandos podem ser chamados através deste modelo de API REST:

- `device-id`: o identificador do dispositivo.
- `interface-name`: o nome da interface da seção implementos no modelo de capacidade do dispositivo.
- `command-name`: o nome do comando.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre modelagem de dispositivos, aqui estão alguns recursos adicionais:

- [Linguagem de definição de gêmeos digitais (DTDL)](https://aka.ms/DTDL)
- [SDK do Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
