---
title: Guia do desenvolvedor de serviços – IoT Plug and Play | Microsoft Docs
description: Descrição de Plug and Play de IoT para desenvolvedores de serviço
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 431ea692ac734a875e27cb8ed4b015155e0e9b91
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042415"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guia do desenvolvedor do serviço de Plug and Play de IoT

O Plug and Play IoT permite criar dispositivos inteligentes que anunciam seus recursos para os aplicativos IoT do Azure. Os dispositivos IoT Plug and Play não exigem configuração manual quando um cliente os conecta aos aplicativos habilitados para IoT Plug and Play.

O Plug and Play IoT permite que você use dispositivos que anunciaram sua ID de modelo com o Hub IoT. Por exemplo, você pode acessar as propriedades e os comandos de um dispositivo diretamente.

Para usar um dispositivo de Plug and Play IoT conectado ao seu hub IoT, use um dos SDKs do serviço IoT ou a API REST do Hub IoT:

## <a name="service-sdks"></a>SDKs do Serviço

Use os SDKs do serviço IoT do Azure em sua solução para interagir com dispositivos e módulos. Por exemplo, você pode usar os SDKs de serviço para ler e atualizar as propriedades de entrelaçamento e invocar comandos. Os idiomas com suporte incluem C#, Java, Node.js e Python.

Os SDKs de serviço permitem que você acesse informações de dispositivo de uma solução, como um desktop ou aplicativo Web. Os SDKs de serviço incluem dois namespaces e modelos de objeto que você pode usar para recuperar a ID do modelo:

- Cliente de serviço do Hub IOT. Esse serviço expõe a ID do modelo como uma propriedade de dispositivo.

- Cliente do serviço de gêmeos digital. A nova API digital gêmeos opera em construções de modelo [DTDL (digital gêmeos Definition Language)](concepts-digital-twin.md) , como componentes, propriedades e comandos. As APIs de teledigital fotodigitais facilitam para os criadores de solução criar soluções de Plug and Play de IoT.

| Plataforma | Cliente de serviço do Hub IoT | Cliente do serviço de gêmeos digital |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [Documentação](/dotnet/api/microsoft.azure.devices) <br/> [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Documentação](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?preserve-view=true&view=azure-java-stable) <br/> [Amostras](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Documentação](/javascript/api/azure-iothub/twin?preserve-view=true&view=azure-node-latest) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Documentação](/javascript/api/azure-iot-digitaltwins-service/?preserve-view=true&view=azure-node-latest) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [Documentação](/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?preserve-view=true&view=azure-python) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Documentação](/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?preserve-view=true&view=azure-python) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="iot-hub-service-client-examples"></a>Exemplos de cliente de serviço do Hub IoT

Esta seção mostra exemplos de C# usando o cliente de serviço do Hub IoT e as classes **registrymanager** e **objectclient** . Use a classe **registrymanager** para interagir com o estado do dispositivo usando o dispositivo gêmeos. Você também pode usar a classe **registrymanager** para [consultar os registros do dispositivo](..\iot-hub\iot-hub-devguide-query-language.md) no Hub IOT. Você usa a classe **userclient** para chamar comandos no dispositivo. O modelo [DTDL](concepts-digital-twin.md) para o dispositivo define as propriedades e os comandos que o dispositivo implementa. Nos trechos de código, a `deviceTwinId` variável contém a ID do dispositivo de plug and Play IOT registrada com o Hub IOT.

### <a name="get-the-device-twin-and-model-id"></a>Obter o dispositivo e a ID do modelo

Para obter o dispositivo de conexão e ID do modelo do dispositivo IoT Plug and Play que se conectou ao seu hub IoT:

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(parameters.HubConnectionString);

Twin twin = await registryManager.GetTwinAsync(deviceTwinId);
Console.WriteLine($"Device twin: \n{JsonConvert.SerializeObject(twin, Formatting.Indented)}");
Console.WriteLine($"Model ID: {twin.ModelId}.");
```

### <a name="update-device-twin"></a>Atualizar dispositivo gêmeo

O trecho de código a seguir mostra como atualizar a `targetTemperature` propriedade em um dispositivo. O exemplo mostra como você precisa obter o ' s ' `ETag` antes de atualizá-lo. A propriedade é definida no componente padrão do dispositivo:

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired["targetTemperature"] = desiredTargetTemperature;

Console.WriteLine($"Update the targetTemperature property to {desiredTargetTemperature}.");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);
```

O trecho a seguir mostra como atualizar a `targetTemperature` propriedade em um componente. O exemplo mostra como você precisa obter o ' s ' `ETag` antes de atualizá-lo. A propriedade é definida na interface **Thermostat1** :

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

var twinPatch = CreatePropertyPatch("targetTemperature", desiredTargetTemperature, "thermostat1");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);

// ...

private static Twin CreatePropertyPatch(string propertyName, object propertyValue, string componentName)
{
    var twinPatch = new Twin();
    twinPatch.Properties.Desired[componentName] = new
    {
        __t = "c"
    };
    twinPatch.Properties.Desired[componentName][propertyName] = JsonConvert.SerializeObject(propertyValue);
    return twinPatch;
}
```

Para uma propriedade em um componente, o patch da propriedade é semelhante ao exemplo a seguir:

```json
{
"sampleComponentName":
  {
    "__t": "c",
    "samplePropertyName": 20
  }
}
```

### <a name="call-command"></a>Chamar comando

O trecho a seguir mostra como invocar o `getMaxMinReport` comando definido em um componente padrão:

```csharp
ServiceClient serviceClient = ServiceClient.CreateFromConnectionString(parameters.HubConnectionString);

var commandInvocation = new CloudToDeviceMethod("getMaxMinReport") { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
  CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);

  Console.WriteLine($"Command getMaxMinReport was invoked." +
      $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine($"Unable to execute command getMaxMinReport on {deviceTwinId}.";
}
```

O trecho a seguir mostra como chamar o `getMaxMinReport` comando em um componente. O comando é definido na interface **Thermostat1** :

```csharp
// Create command name to invoke for component. The command is formatted as <component name>*<command name>
string commandToInvoke = "thermostat1*getMaxMinReport";
var commandInvocation = new CloudToDeviceMethod(commandToInvoke) { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
    CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);
    Console.WriteLine($"Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
}
```

## <a name="digital-twin-examples"></a>Exemplos de "foto-digital"

Você usa a classe **DigitalTwinClient** para interagir com o estado do dispositivo usando o gêmeos digital. O modelo [DTDL](concepts-digital-twin.md) para o dispositivo define as propriedades e os comandos que o dispositivo implementa.

Esta seção mostra exemplos de C# usando a API digital gêmeos. Os trechos de código a seguir usam as seguintes classes para representar as termostato digitais dos dispositivos do controlador de temperatura e de uso:

```csharp
using Microsoft.Azure.Devices.Serialization;
using Newtonsoft.Json;
using System;

namespace Microsoft.Azure.Devices.Samples
{
  internal class ThermostatTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new ThermostatMetadata Metadata { get; set; }

    [JsonProperty("maxTempSinceLastReboot")]
    public double? MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public double? TargetTemperature { get; set; }
  }

  internal class ThermostatMetadata : DigitalTwinMetadata
  {
    [JsonProperty("maxTempSinceLastReboot")]
    public ReportedPropertyMetadata MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public WritableProperty TargetTemperature { get; set; }
  }

  internal class ReportedPropertyMetadata
  {
    [JsonProperty("lastUpdateTime")]
    public DateTimeOffset LastUpdateTime { get; set; }
  }

  internal class TemperatureControllerTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new TemperatureControllerMetadata Metadata { get; set; }

    [JsonProperty("serialNumber")]
    public string SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public ThermostatTwin Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public ThermostatTwin Thermostat2 { get; set; }
  }

  internal class TemperatureControllerMetadata : DigitalTwinMetadata
  {
    [JsonProperty("serialNumber")]
    public ReportedPropertyMetadata SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public WritableProperty Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public WritableProperty Thermostat2 { get; set; }
  }
}
```

A `digitalTwinId` variável contém a ID do dispositivo de plug and Play IOT registrada com o Hub IOT.

### <a name="get-the-digital-twin-and-model-id"></a>Obter a ID de modelo e a. digital

Para obter a ID de modelo e conexão digital do dispositivo IoT Plug and Play que se conectou ao seu hub IoT:

```csharp
DigitalTwinClient digitalTwinClient = DigitalTwinClient.CreateFromConnectionString(parameters.HubConnectionString);
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
ThermostatTwin thermostatTwin = getDigitalTwinResponse.Body;
Console.WriteLine($"Model ID: {thermostatTwin.Metadata.ModelId}.");
Console.WriteLine($"Digital Twin: \n{JsonConvert.SerializeObject(thermostatTwin, Formatting.Indented)}");
```

### <a name="update-digital-twin"></a>Atualizar atualização digital

O trecho de código a seguir mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A propriedade é definida no componente padrão do dispositivo:

```csharp
var updateOperation = new UpdateOperationsUtility();

int desiredTargetTemperature = 60;

// Get the current value of the targetTemperature property
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
double? currentTargetTemperature = getDigitalTwinResponse.Body.TargetTemperature;

// Has the targetTemperature property previously been set?
if (currentTargetTemperature != null)
{
  // Update the existing property
  // Prepend the property path with a '/'
  updateOperation.AppendReplacePropertyOp($"/targetTemperature", desiredTargetTemperature);
}
else
{
  // Add a new property
  // Prepend the property path with a '/'
  updateOperation.AppendAddPropertyOp($"/targetTemperature", desiredTargetTemperature);
}

// Update the targetTemperature property on the digital twin
HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

O trecho a seguir mostra como atualizar a `targetTemperature` propriedade em um componente. A propriedade é definida no componente **Thermostat1** :

```csharp
int desiredTargetTemperature = 60;

var updateOperation = new UpdateOperationsUtility();

// Look at when the property was updated and what was it set to.
HttpOperationResponse<TemperatureControllerTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
  .GetDigitalTwinAsync<TemperatureControllerTwin>(digitalTwinId);

ThermostatTwin thermostat1 = getDigitalTwinResponse.Body.Thermostat1;

if (thermostat1 != null)
{
  // Thermostat1 is present in the TemperatureController twin. You can add/replace the component-level property "targetTemperature"
  double? currentComponentTargetTemperature = getDigitalTwinResponse.Body.Thermostat1.TargetTemperature;
  if (currentComponentTargetTemperature != null)
  {
      DateTimeOffset targetTemperatureDesiredLastUpdateTime = getDigitalTwinResponse.Body.Thermostat1.Metadata.TargetTemperature.LastUpdateTime;

      // The property path to be replaced should be prepended with a '/'
      updateOperation.AppendReplacePropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
  else
  {
      // The property path to be added should be prepended with a '/'
      updateOperation.AppendAddPropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
}
else
{
    // Thermostat1 is not present in the TemperatureController twin. Add the component.
    var componentProperty = new Dictionary<string, object> { { "targetTemperature", desiredTargetTemperature }, { "$metadata", new object() } };

    // The property path to be replaced should be prepended with a '/'
    updateOperation.AppendAddComponentOp("/thermostat1", componentProperty);
}

HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

### <a name="call-command"></a>Chamar comando

O trecho a seguir mostra como invocar o `getMaxMinReport` comando definido em um componente padrão:

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
  HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
    .InvokeCommandAsync(digitalTwinId, "getMaxMinReport", JsonConvert.SerializeObject(since));

  Console.WriteLine($"Command getMaxMinReport was invoked. \nDevice returned status: {invokeCommandResponse.Body.Status}." +
    $"\nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
  if (e.Response.StatusCode == HttpStatusCode.NotFound)
  {
    Console.WriteLine($"Unable to execute command getMaxMinReport on {digitalTwinId}.");
  }
}
```

O trecho a seguir mostra como chamar o `getMaxMinReport` comando em um componente. O comando é definido na interface **Thermostat1** :

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
    HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
        .InvokeComponentCommandAsync(digitalTwinId, "thermostat1", "getMaxMinReport", JsonConvert.SerializeObject(since));

    Console.WriteLine("Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {invokeCommandResponse.Body.Status}. \nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
    if (e.Response.StatusCode == HttpStatusCode.NotFound)
    {
        Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
    }
}
```

## <a name="rest-api"></a>API REST

Os exemplos a seguir usam a API REST do Hub IoT para interagir com um dispositivo de Plug and Play de IoT conectado. A versão atual da API é `2020-09-30` . Acrescente `?api-version=2020-09-30` às suas chamadas do PI REST.

> [!NOTE]
> Atualmente, o módulo gêmeos não tem suporte da `digitalTwins` API.

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

## <a name="read-device-telemetry"></a>Ler telemetria do dispositivo

Os dispositivos IoT Plug and Play enviam a telemetria definida no modelo DTDL para o Hub IoT. Por padrão, o Hub IoT roteia a telemetria para um ponto de extremidade de hubs de eventos, onde você pode consumi-la. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade](../iot-hub/iot-hub-devguide-messages-d2c.md).

O trecho de código a seguir mostra como ler a telemetria do ponto de extremidade de hubs de eventos padrão. O código neste trecho é obtido do início rápido do Hub IoT [Enviar telemetria de um dispositivo para um hub IOT e lê-lo com um aplicativo de back-end](../iot-hub/quickstart-send-telemetry-dotnet.md):

```csharp
await using EventHubConsumerClient consumer = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, connectionString, EventHubName);

Console.WriteLine("Listening for messages on all partitions");

try
{
    await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync(cancellationToken))
    {
        Console.WriteLine("Message received on partition {0}:", partitionEvent.Partition.PartitionId);

        string data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
        Console.WriteLine("\t{0}:", data);

        Console.WriteLine("Application properties (set by device):");
        foreach (var prop in partitionEvent.Data.Properties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }

        Console.WriteLine("System properties (set by IoT Hub):");
        foreach (var prop in partitionEvent.Data.SystemProperties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }
    }
}
catch (TaskCanceledException)
{
    // This is expected when the token is signaled; it should not be considered an
    // error in this scenario.
}
```

A saída a seguir do código anterior mostra a telemetria de temperatura enviada pelo dispositivo **termostato** plug and Play IOT de não componente que tem apenas o componente padrão. A `dt-dataschema` Propriedade System mostra a ID do modelo:

```cmd/sh
Message received on partition 1:
        { "temperature": 25.5 }:
Application properties (set by device):
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:30:58
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:Thermostat;1
        content-type: application/json
        content-encoding: utf-8
```

A saída a seguir do código anterior mostra a telemetria de temperatura enviada pelo dispositivo de Plug and Play IOT **TemperatureController** de vários componentes. A `dt-subject` Propriedade System mostra o nome do componente que enviou a telemetria. Neste exemplo, os dois componentes são `thermostat1` e `thermostat2` , conforme definido no modelo DTDL. A `dt-dataschema` Propriedade System mostra a ID do modelo:

```cmd/sh
Message received on partition 1:
        {"temperature":11.1}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat1
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
Message received on partition 1:
        {"temperature":41.2}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat2
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-device-twin-change-notifications"></a>Ler notificações de alteração de troca de dispositivo

Você pode configurar o Hub IoT para gerar notificações de alteração de troca de dispositivo para rotear para um ponto de extremidade com suporte. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade > eventos de não telemetria](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no trecho de código C# anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de dispositivo para um dispositivo termostato sem componente. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Message received on partition 1:
        {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":9.6,"$metadata":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z"}},"$version":2}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:17:41.7408552+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:17:41
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 61394e8ba7d
        content-type: application/json
        content-encoding: utf-8
```

O código mostrado no trecho de código C# anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de dispositivo de troca para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente termostato gera notificações. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Message received on partition 1:
        {"version":5,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":9.6},"$metadata":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","thermostat1":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","__t":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"}}},"$version":4}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:27:59.5159720+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:27:59
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 615051f364e
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-digital-twin-change-notifications"></a>Ler notificações de alteração de troca digital

Você pode configurar o Hub IoT para gerar notificações de alteração de troca digital para rotear para um ponto de extremidade com suporte. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade > eventos de não telemetria](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no trecho de código C# anterior gera a seguinte saída quando o Hub IoT gera notificações de alteração de troca digital para um dispositivo termostato sem componente. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-06T10:39:16.0209836Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":34.9}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:39:16.0209836+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:39:16
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 6169857bf8c
        content-type: application/json-patch+json
        content-encoding: utf-8
```

O código mostrado no trecho de código C# anterior gera a seguinte saída quando o Hub IoT gera notificações de alteração de troca digital para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente termostato gera notificações. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-06T10:41:44.8312666Z"}},"maxTempSinceLastReboot":29.1}}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:41:44.8312666+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:41:44
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 616f108f0e3
        content-type: application/json-patch+json
        content-encoding: utf-8
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a modelagem de dispositivo, aqui estão alguns recursos adicionais:

- [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK do Dispositivo C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](/rest/api/iothub/device)
- [Componentes do modelo](./concepts-components.md)
- [Instalar e usar as ferramentas de criação do DTDL](howto-use-dtdl-authoring-tools.md)