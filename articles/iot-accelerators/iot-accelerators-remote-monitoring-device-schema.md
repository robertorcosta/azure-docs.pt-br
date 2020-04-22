---
title: Esquema de dispositivo na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este artigo descreve o esquema JSON que define um dispositivo simulado na solução de monitoramento remoto.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ac681bb13ccea49c7a2f566a6fcdb6adb8cec5bb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683737"
---
# <a name="understand-the-device-model-schema"></a>Compreender o esquema de modelo do dispositivo

É possível usar dispositivos simulados na solução de monitoramento remoto para testar o comportamento. A solução de Monitoramento Remoto inclui um serviço de simulação de dispositivo para executar dispositivos simulados. Quando você implanta a solução de monitoramento remoto, uma coleção de dispositivos simulados é provisionada automaticamente. Você pode personalizar os dispositivos simulados existentes ou criar o seu próprio.

Este artigo descreve o esquema de modelo do dispositivo que especifica os recursos e o comportamento de um dispositivo simulado. O modelo de dispositivo é armazenado em um arquivo JSON.

> [!NOTE]
> Esse esquema de modelo de dispositivo é apenas para dispositivos simulados hospedados no serviço de simulação de dispositivo. Se você quer criar um dispositivo real, confira [Conectar seu dispositivo ao acelerador de solução de Monitoramento Remoto](iot-accelerators-connecting-devices.md).

Os artigos a seguir estão relacionados ao artigo atual:

* [Implementar o comportamento de modelo do dispositivo](iot-accelerators-remote-monitoring-device-behavior.md) descreve os arquivos JavaScript que são utilizados para implementar o comportamento de um dispositivo simulado.
* [Criar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md) reúne tudo e mostra como implementar um novo tipo de dispositivo simulado para sua solução.

Neste artigo, você aprenderá como:

>[!div class="checklist"]
> * Usar um arquivo JSON para definir um modelo de dispositivo simulado
> * Especificar as propriedades do dispositivo simulado
> * Especificar a telemetria que o dispositivo simulado envia
> * Especificar os métodos de nuvem para dispositivo em que o dispositivo responde

## <a name="the-parts-of-the-device-model-schema"></a>As partes do esquema de modelo do dispositivo

Cada modelo do dispositivo, como um resfriador ou caminhão, define um tipo de dispositivo para que o serviço de simulação pode simular. Cada modelo do dispositivo é armazenado em um arquivo JSON com o seguinte esquema de nível superior:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

É possível exibir os arquivos de esquema para os dispositivos simulados padrão na [pasta devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) no GitHub.

A tabela a seguir descreve as entradas de esquema de nível superior:

| Entrada de esquema | Descrição |
| -- | --- |
| `SchemaVersion` | A versão do esquema sempre é `1.0.0` e específica para o formato desse arquivo. |
| `Id` | Uma ID exclusiva para esse modelo do dispositivo. |
| `Version` | Identifica a versão do modelo do dispositivo. |
| `Name` | Um nome amigável para o modelo do dispositivo. |
| `Description` | Uma descrição do modelo do dispositivo. |
| `Protocol` | O protocolo de conexão que o dispositivo usa. Pode ser um `AMQP`, `MQTT` e `HTTP`. |

As seções a seguir descrevem as outras seções no esquema JSON:

## <a name="simulation"></a>Simulação

Na `Simulation` seção, você define o estado interno do dispositivo simulado. Todos os valores de telemetria enviados pelo dispositivo devem ser parte desse estado do dispositivo.

A definição do estado do dispositivo tem dois elementos:

* `InitialState` define valores iniciais para todas as propriedades do objeto de estado do dispositivo.
* `Script` identifica um arquivo JavaScript que executa em uma programação para atualizar o estado do dispositivo. É possível usar esse arquivo de script para randomizar os valores de telemetria enviados pelo dispositivo.

Para saber mais sobre o arquivo JavaScript que atualiza o objeto de estado do dispositivo, consulte [Compreender o comportamento do modelo do dispositivo](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

O exemplo a seguir mostra a definição do objeto de estado do dispositivo para um dispositivo resfriador simulado:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

O serviço de simulação executa o arquivo **chiller-01-state.js** a cada cinco segundos para atualizar o estado do dispositivo. É possível ver os arquivos JavaScript para os dispositivos simulados padrão na [pasta de scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) no GitHub. Por convenção, esses arquivos JavaScript têm o sufixo **-state** para distingui-los dos arquivos que implementam comportamentos do método.

## <a name="properties"></a>Propriedades

A seção `Properties` do esquema define os valores de propriedade que o dispositivo informa sobre a solução. Por exemplo:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Quando a solução inicia, todos os dispositivos simulados são consultados para compilar uma lista de valores `Type` para usar na interface do usuário. A solução usa as propriedades `Latitude` e `Longitude` para adicionar o local do dispositivo ao mapa no painel de controle.

## <a name="telemetry"></a>Telemetria

A matriz `Telemetry` lista todos os tipos de telemetria que o dispositivo simulado envia para a solução.

O exemplo a seguir envia uma mensagem de telemetria JSON a cada 10 segundos com dados `floor`, `vibration` e `temperature` dos sensores do elevador:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` define a estrutura da mensagem JSON enviada pelo dispositivo simulado. Os espaços reservados em `MessageTemplate` usam a sintaxe `${NAME}` onde `NAME` é uma chave do [objeto de estado do dispositivo](#simulation). As cadeias de caracteres deverão ser cotadas, os números não deverão.

`MessageSchema` define o esquema da mensagem enviada pelo dispositivo simulado. O esquema de mensagens também é publicado no Hub IoT para permitir que os aplicativos back-end reutilizem as informações para interpretar a telemetria recebida.

Atualmente, é possível usar somente os esquemas de mensagens JSON. Os campos listados no esquema podem ser dos tipos a seguir:

* Objeto - serializado usando JSON
* Binário - serializado usando base64
* Texto
* Boolean
* Integer
* Double
* Datetime

Para enviar mensagens de telemetria em intervalos diferentes, adicione vários tipos de telemetria à matriz `Telemetry`. O exemplo a seguir envia dados de temperatura e umidade a cada 10 segundos e o estado da luz a cada minuto:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Um dispositivo simulado pode responder a métodos de nuvem para dispositivo chamados de um hub IoT. A seção `CloudToDeviceMethods` no arquivo de esquema de modelo do dispositivo:

* Define os métodos aos quais o dispositivo simulado pode responder.
* Identifica o arquivo JavaScript que contém a lógica a ser executada.

O dispositivo simulado envia a lista de métodos que ele suporta para o hub IoT ao qual está conectado.

Para saber mais sobre o arquivo JavaScript que implementa o comportamento do dispositivo, consulte [Compreender o comportamento do modelo do dispositivo](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

O exemplo a seguir especifica três métodos com suporte e os arquivos JavaScript que implementam esses métodos:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

É possível ver os arquivos JavaScript para os dispositivos simulados padrão na [pasta de scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) no GitHub. Por convenção, esses arquivos JavaScript possuem o sufixo **-method** para distingui-los dos arquivos que implementam o comportamento de estado.

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu como criar seu próprio modelo de dispositivo simulado personalizado. Este artigo mostrou como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usar um arquivo JSON para definir um modelo de dispositivo simulado
> * Especificar as propriedades do dispositivo simulado
> * Especificar a telemetria que o dispositivo simulado envia
> * Especificar os métodos de nuvem para dispositivo em que o dispositivo responde

Agora que você aprendeu sobre o esquema JSON, a próxima etapa sugerida é saber como [implementar o comportamento do dispositivo simulado](iot-accelerators-remote-monitoring-device-behavior.md).

Para obter informações do desenvolvedor sobre a solução de Monitoramento Remoto, confira:

* [Guia de Referência do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de solução de problemas do desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
