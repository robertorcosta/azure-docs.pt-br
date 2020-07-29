---
title: Implementar descoberta de modelo de visualização de IoT Plug and Play | Microsoft Docs
description: Como um construtor de soluções, saiba como você pode implementar a descoberta de modelo de Plug and Play de IoT em sua solução.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337374"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementar a descoberta do modelo de visualização de IoT Plug and Play em uma solução de IoT

Este artigo descreve como, como um Solution Builder, você pode implementar a descoberta de modelo do IoT Plug and Play preview em uma solução de IoT. A descoberta de modelo descreve como:

- Os dispositivos IoT Plug and Play registram sua ID de modelo.
- Uma solução de IoT recupera as interfaces implementadas pelo dispositivo.

Há duas categorias amplas de solução de IoT:

- Uma *solução de IOT criada por finalidade* funciona com um conjunto conhecido de modelos de dispositivo de plug and Play de IOT.

- Uma *solução de IOT controlada por modelos* pode funcionar com qualquer dispositivo de plug and Play de IOT. Criar uma solução controlada por modelo é mais complexo, mas o benefício é que sua solução funciona com qualquer dispositivo adicionado no futuro.

    Para criar uma solução de IoT controlada por modelos, você precisa criar lógica em relação aos primitivos de interface de Plug and Play de IoT: telemetria, propriedades e comandos. A lógica da solução representa um dispositivo combinando vários recursos de telemetria, propriedade e comando.

Este artigo descreve como implementar a descoberta de modelo em ambos os tipos de solução.

## <a name="model-discovery"></a>Descoberta de modelos

Para descobrir o modelo que um dispositivo implementa, uma solução pode obter a ID do modelo usando a descoberta baseada em eventos ou a descoberta baseada em entrelaçamento:

### <a name="event-based-discovery"></a>Descoberta baseada em evento

Quando um dispositivo de Plug and Play IoT se conecta ao Hub IoT, ele registra o modelo que ele implementa. Esse registro resulta em uma notificação de [evento de alteração de troca digital](concepts-digital-twin.md#digital-twin-change-events) . Para saber como habilitar o roteamento para eventos de computação digital, consulte [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

A solução pode usar o evento mostrado no trecho a seguir para saber mais sobre o dispositivo IoT Plug and Play que está se conectando e obter sua ID de modelo:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

Esse evento é disparado quando a ID do modelo do dispositivo é adicionada ou atualizada.

### <a name="twin-based-discovery"></a>Descoberta baseada em entrelaçamento

Se a solução quiser saber sobre os recursos de um determinado dispositivo, ela poderá usar a API de [obtenção digital](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) de dados para recuperar as informações.

No trecho de código digital a seguir, `$metadata.$model` contém a ID do modelo de um dispositivo de plug and Play IOT:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

A solução também pode usar **Get** fileto retrieve ID do modelo do dispositivo "/", conforme mostrado no trecho de código a seguir:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>Resolução de modelo

Uma solução usa a resolução de modelo para obter acesso às interfaces que compõem um modelo a partir da ID do modelo. 

- As soluções podem optar por armazenar essas interfaces como arquivos em uma pasta local. 
- As soluções podem usar o [repositório de modelos](concepts-model-repository.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a descoberta de modelos de uma solução de IoT, saiba mais sobre a [plataforma IOT do Azure](overview-iot-plug-and-play.md) para usar outros recursos para sua solução.

- [Interagir com um dispositivo de sua solução](quickstart-service-node.md)
- [API REST de monodigital de IoT](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Gerenciador de IoT do Azure](howto-use-iot-explorer.md)
