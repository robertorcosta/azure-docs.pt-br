---
title: Guia do desenvolvedor de serviços – IoT Plug and Play | Microsoft Docs
description: Descrição de Plug and Play de IoT para desenvolvedores de serviço
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f082e4d4c6c71e460842f80a5aa17130b6a41279
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614216"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guia do desenvolvedor do serviço de Plug and Play de IoT

O Plug and Play IoT permite criar dispositivos inteligentes que anunciam seus recursos para os aplicativos IoT do Azure. Os dispositivos IoT Plug and Play não exigem configuração manual quando um cliente os conecta aos aplicativos habilitados para IoT Plug and Play.

O Plug and Play IoT permite que você use dispositivos que anunciaram sua ID de modelo com o Hub IoT. Por exemplo, você pode acessar as propriedades e os comandos de um dispositivo diretamente.

Para usar um dispositivo de Plug and Play IoT conectado ao seu hub IoT, use um dos SDKs do serviço IoT ou a API REST do Hub IoT:

## <a name="service-sdks"></a>SDKs do Serviço

Use os SDKs do serviço IoT do Azure em sua solução para interagir com dispositivos e módulos. Por exemplo, você pode usar os SDKs de serviço para ler e atualizar as propriedades de entrelaçamento e invocar comandos. Os idiomas com suporte incluem C#, Java, Node.js e Python.

Os SDKs de serviço permitem que você acesse informações de dispositivo de uma solução, como um desktop ou aplicativo Web. Os SDKs de serviço incluem dois namespaces e modelos de objeto que você pode usar para recuperar a ID do modelo:

- Cliente de serviço do Hub IOT. Esse serviço expõe a ID do modelo como uma propriedade de dispositivo.

- Cliente do serviço de gêmeos digital. A nova API digital gêmeos opera em construções de alto nível, como componentes, propriedades e comandos que são definidos um modelo de linguagem de definição de gêmeos digital. As APIs de teledigital fotodigitais facilitam para os criadores de solução criar soluções de Plug and Play de IoT.

| Plataforma | Cliente de serviço do Hub IoT | Cliente do serviço de gêmeos digital |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [Documentação](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Documentação](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Amostras](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Documentação](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Documentação](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [Documentação](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Documentação](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

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

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a modelagem de dispositivo, aqui estão alguns recursos adicionais:

- [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK do Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Componentes do modelo](./concepts-components.md)
- [Instalar e usar as ferramentas de criação do DTDL](howto-use-dtdl-authoring-tools.md)
