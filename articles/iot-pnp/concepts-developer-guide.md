---
title: Guia do desenvolvedor-visualização de Plug and Play de IoT | Microsoft Docs
description: Descrição de Plug and Play de IoT para desenvolvedores
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9e6d13fedbfa495448164c1354868e12992dd71c
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856031"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>Guia do desenvolvedor da visualização do IoT Plug and Play

O IoT Plug and Play Preview permite que você crie dispositivos inteligentes que anunciam seus recursos para os aplicativos de IoT do Azure. Os dispositivos IoT Plug and Play não exigem configuração manual quando um cliente os conecta aos aplicativos habilitados para IoT Plug and Play.

Este guia descreve as etapas básicas necessárias para criar um dispositivo que segue as [convenções de plug and Play IOT](concepts-convention.md)e as APIs REST disponíveis que você pode usar para interagir com o dispositivo.

Para criar um dispositivo de Plug and Play de IoT, siga as etapas desseguidos:

1. Verifique se o dispositivo está usando o protocolo MQTT ou MQTT sobre Websockets para se conectar ao Hub IoT do Azure.
1. Crie um modelo de [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) para descrever seu dispositivo. Para saber mais, confira [entender os componentes em modelos de plug and Play IOT](concepts-components.md).
1. Atualize seu dispositivo para anunciar o `model-id` como parte da conexão do dispositivo.
1. Implementar telemetria, propriedades e comandos usando as [convenções de plug and Play IOT](concepts-convention.md)

Depois que a implementação do dispositivo estiver pronta, use o [Azure IOT Explorer](howto-use-iot-explorer.md) para validar que o dispositivo segue as convenções de plug and Play de IOT.

> [!Tip]
> Todos os fragmentos de código neste artigo usam C#, mas os conceitos são aplicáveis a qualquer um dos SDKs disponíveis para C, Python, Node e Java.

## <a name="model-id-announcement"></a>Comunicado de ID do modelo

Para anunciar a ID do modelo, o dispositivo deve incluí-lo nas informações de conexão:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

A nova `ClientOptions` sobrecarga está disponível em todos os `DeviceClient` métodos usados para inicializar uma conexão.

O comunicado de ID do modelo foi adicionado às próximas versões dos SDKs

|SDK|Versão|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Nó|1.17.0|
|Python|2.1.4|

## <a name="dps-payload"></a>Carga de DPS

Os dispositivos que usam o [DPS (serviço de provisionamento de dispositivos)](/iot-dps/) podem incluir o `modelId` a ser usado durante o processo de provisionamento usando a carga JSON a seguir.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementar telemetria, propriedades e comandos

Conforme descrito em [entender os componentes nos modelos de plug and Play IOT](concepts-components.md), os criadores de dispositivos devem decidir se desejam usar componentes para descrever seus dispositivos. Ao usar componentes, os dispositivos devem seguir as regras descritas nesta seção.

### <a name="telemetry"></a>Telemetria

Modelos sem componentes não exigem nenhuma propriedade especial.

Ao usar componentes, os dispositivos devem definir uma propriedade de mensagem com o nome do componente:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Propriedades somente leitura

Modelos sem componentes não exigem qualquer construção especial:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Ao usar componentes, as propriedades devem ser criadas dentro do nome do componente:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Propriedades graváveis

Essas propriedades podem ser definidas pelo dispositivo ou atualizadas pela solução. Se a solução atualizar uma propriedade, o cliente receberá uma notificação como um retorno de chamada no `DeviceClient` . Para seguir as convenções de Plug and Play IoT, o dispositivo deve informar ao serviço que a propriedade foi recebida com êxito.

#### <a name="report-a-writable-property"></a>Relatar uma propriedade gravável

Quando um dispositivo relata uma propriedade gravável, ele deve incluir os `ack` valores definidos nas convenções.

Para relatar uma propriedade gravável sem componentes:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Para relatar uma propriedade gravável de um componente, o ' myQuery ' deve incluir um marcador:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Assinar as atualizações da propriedade desejada

Os serviços podem atualizar as propriedades desejadas que disparam uma notificação nos dispositivos conectados. Essa notificação inclui as propriedades desejadas atualizadas, incluindo o número de versão que identifica a atualização. Os dispositivos devem responder com a mesma `ack` mensagem que as propriedades relatadas.

Modelos sem componentes veem a propriedade única e criam o relatório `ack` com a versão recebida:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

O dispositivo "up" mostra a propriedade nas seções desejadas e relatadas:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Modelos com componentes recebem as propriedades desejadas encapsuladas com o nome do componente e devem relatar a `ack` Propriedade relatada:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

O dispositivo "up para componentes" mostra as seções desejadas e relatadas da seguinte maneira:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Comandos

Modelos sem componentes recebem o nome do comando conforme ele foi invocado pelo serviço.

Modelos com componentes receberão o nome do comando prefixado com o componente e o `*` separador.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Cargas de solicitação e resposta

Os comandos usam tipos para definir suas cargas de solicitação e resposta. Um dispositivo deve desserializar o parâmetro de entrada de entrada e serializar a resposta. O exemplo a seguir mostra como implementar um comando com tipos complexos definidos nas cargas:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Os trechos de código a seguir mostram como um dispositivo implementa essa definição de comando, incluindo os tipos usados para habilitar a serialização e desserialização:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> Os nomes de solicitação e resposta não estão presentes nas cargas serializadas transmitidas pela conexão.

## <a name="interact-with-the-device"></a>Interagir com o dispositivo 

O Plug and Play IoT permite que você use dispositivos que anunciaram sua ID de modelo com o Hub IoT. Por exemplo, você pode acessar as propriedades e os comandos de um dispositivo diretamente.

Para usar um dispositivo de Plug and Play IoT conectado ao seu hub IoT, use a API REST do Hub IoT ou um dos SDKs da linguagem IoT. Os exemplos a seguir usam a API REST do Hub IoT. A versão atual da API é `2020-05-31-preview` . Acrescente `?api-version=2020-05-31` às suas chamadas do PI REST.

Se o dispositivo termostato for chamado `t-123` , você obterá todas as propriedades em todas as interfaces implementadas pelo dispositivo com uma chamada Get da API REST:

```REST
GET /digitalTwins/t-123
```

Essa chamada incluirá a propriedade JSON `$metadata.$model` com a ID de modelo anunciada pelo dispositivo.

Todas as propriedades em todas as interfaces são acessadas com o `GET /DigitalTwin/{device-id}` modelo de API REST, em que `{device-id}` é o identificador do dispositivo:

```REST
GET /digitalTwins/{device-id}
```

Você pode chamar os comandos do dispositivo IoT Plug and Play diretamente. Se o `Thermostat` componente no `t-123` dispositivo tiver um `restart` comando, você poderá chamá-lo com uma pós-chamada à API REST:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Em geral, os comandos podem ser chamados por meio desse modelo de API REST:

- `device-id`: o identificador do dispositivo.
- `component-name`: o nome da interface da seção implementações no modelo de funcionalidade do dispositivo.
- `command-name`: o nome do comando.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a modelagem de dispositivo, aqui estão alguns recursos adicionais:

- [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK do Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Componentes do modelo](./concepts-components.md)
