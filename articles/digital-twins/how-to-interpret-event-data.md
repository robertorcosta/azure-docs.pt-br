---
title: Interpretar dados de eventos
titleSuffix: Azure Digital Twins
description: Veja como interpretar diversos tipos de evento e as respectivas mensagens de notificação distintas.
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1901104aa05b4e7ea3a318ee8e886c745f2a6eb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936037"
---
# <a name="understand-event-data"></a>Entender os dados do evento

Os diversos eventos nos Gêmeos Digitais do Azure produzem **notificações**, que permitem que o back-end da solução seja percebido com a ocorrência de ações distintas. Em seguida, eles são [roteados](concepts-route-events.md) para diferentes locais dentro e fora dos Gêmeos Digitais do Azure que podem usar essas informações para tomar medidas.

Há vários tipos de notificações que podem ser geradas e as mensagens de notificação podem parecer diferentes, dependendo do tipo de evento gerado. Este artigo fornece detalhes sobre os diversos tipos de mensagens e o que elas podem ter.

Este gráfico mostra os diferentes tipos de notificação:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>Estrutura de notificação

Em geral, as notificações são compostas por duas partes: o cabeçalho e o corpo. 

### <a name="event-notification-headers"></a>Cabeçalho da notificação de eventos

Os cabeçalhos de mensagens de notificação são representados com pares chave-valor. Dependendo do protocolo usado (MQTT, AMQP ou HTTP), os cabeçalhos da mensagem serão serializados de modo distinto. Esta seção discute informações gerais de cabeçalho para mensagens de notificação, independentemente do protocolo específico e da serialização escolhidos.

Algumas notificações estão em conformidade com o padrão [CloudEvents](https://cloudevents.io/). A conformidade com o CloudEvents acontece da seguinte forma:
* As notificações emitidas de dispositivos continuam seguindo as especificações existentes para as notificações
* As notificações processadas e emitidas pelo Hub IoT continuam seguindo as especificações existentes para notificação, exceto quando o Hub IoT opta por dar suporte ao CloudEvents, como por meio da Grade de Eventos
* As notificações emitidas dos [gêmeos digitais](concepts-twins-graph.md) com um [modelo](concepts-models.md) estão em conformidade com o CloudEvents
* As notificações processadas e emitidas pelos Gêmeos Digitais do Azure estão em conformidade com o CloudEvents

Os serviços precisam adicionar um número de sequência em todas as notificações para indicar a ordem delas ou manter a própria ordenação de outra maneira. 

As notificações emitidas pelos Gêmeos Digitais do Azure para a Grade de Eventos serão formatadas automaticamente para o esquema CloudEvents ou EventGridEvent, dependendo do tipo de esquema definido no tópico da grade de eventos. 

Os atributos de extensão nos cabeçalhos serão adicionados como propriedades no esquema da Grade de Eventos dentro do conteúdo. 

### <a name="event-notification-bodies"></a>Corpos da notificação de eventos

Os corpos das mensagens de notificação são descritos aqui no JSON. Dependendo da serialização desejada para o corpo da mensagem (como JSON, CBOR, Protobuf etc.), o corpo da mensagem pode ser serializado de modo distinto.

O conjunto de campos que o corpo contém varia de acordo com diversos tipos de notificação. Aqui estão dois corpos de mensagens de exemplo, para ter uma ideia de como eles geralmente se parecem e do que eles podem incluir.

Mensagem de telemetria:

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

Mensagem de notificação do ciclo de vida:

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

As seções a seguir fornecem mais detalhes sobre os diferentes tipos de notificações emitidas pelo Hub IoT e pelos Gêmeos Digitais do Azure (ou outros serviços do Azure IoT). Você lerá sobre o que dispara cada tipo de notificação e o conjunto de campos incluídos em cada tipo de corpo de notificação.

## <a name="digital-twin-change-notifications"></a>Notificações de alteração do gêmeo digital

As **notificações de alteração do gêmeo digital** são disparadas quando um gêmeo digital está sendo atualizado, como:
* Quando os valores de propriedade ou de metadados são alterados.
* Quando os metadados de componente ou o gêmeo digital são alterados. Um exemplo desse cenário é a alteração do modelo de um gêmeo digital.

### <a name="properties"></a>Propriedades

Aqui estão os campos no corpo de uma notificação de alteração do gêmeo digital.

| Nome    | Valor |
| --- | --- |
| `id` | Identificador da notificação, como um UUID ou um contador mantido pelo serviço. `source` + `id` é exclusivo para cada evento distinto |
| `source` | Nome do hub IoT ou da instância dos Gêmeos Digitais do Azure, como *myhub.azure-devices.net* ou *mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | *1.0*<br>A mensagem está de acordo com esta versão da [especificação CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | ID do gêmeo digital |
| `time` | Carimbo de data/hora do momento de ocorrência da operação no gêmeo digital |
| `traceparent` | Um contexto de rastreamento W3C para o evento |

### <a name="body-details"></a>Detalhes do corpo

O corpo da notificação `Twin.Update` é um documento de patch JSON que contém a atualização para o gêmeo digital.

Por exemplo, digamos que um gêmeo digital tenha sido atualizado usando o patch a seguir.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

A notificação correspondente (se executada de modo síncrono pelo serviço, como a atualização de um gêmeo digital pelos Gêmeos Digitais do Azure) teria um corpo como:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="digital-twin-lifecycle-notifications"></a>Notificações do ciclo de vida do gêmeo digital

Todas as notificações de emissão de [gêmeos digitais](concepts-twins-graph.md), independentemente de elas representarem [dispositivos de Hub IoT nos Gêmeos Digitais do Azure](how-to-ingest-iot-hub-data.md) ou não. Isso ocorre por causa das **notificações do ciclo de vida**, que são sobre o próprio gêmeo digital.

As notificações do ciclo de vida são disparadas quando:
* Um gêmeo digital é criado
* Um gêmeo digital é excluído

### <a name="properties"></a>Propriedades

Aqui estão os campos no corpo de uma notificação do ciclo de vida.

| Nome | Valor |
| --- | --- |
| `id` | Identificador da notificação, como um UUID ou um contador mantido pelo serviço. `source` + `id` é exclusivo para cada evento distinto. |
| `source` | Nome do hub IoT ou da instância dos Gêmeos Digitais do Azure, como *myhub.azure-devices.net* ou *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1.0*<br>A mensagem está de acordo com esta versão da [especificação CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID do gêmeo digital |
| `time` | Carimbo de data/hora do momento de ocorrência da operação no gêmeo |
| `traceparent` | Um contexto de rastreamento W3C para o evento |

### <a name="body-details"></a>Detalhes do corpo

O corpo é o gêmeo digital afetado, representado no formato JSON. O esquema para isso é o *Recurso dos Gêmeos Digitais 7.1*.

Para eventos de criação, o conteúdo reflete o estado de um gêmeo após a criação do recurso. Portanto, ele deve incluir todos os elementos gerados pelo sistema, assim como uma chamada `GET`.

Aqui está um exemplo de um corpo para um dispositivo de [IoT PnP (IoT Plug and Play)](../iot-pnp/overview-iot-plug-and-play.md), com componentes e nenhuma propriedade de nível superior. As propriedades que não fazem sentido para dispositivos (como propriedades relatadas) devem ser omitidas.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Aqui está outro exemplo de um gêmeo digital. Esse é baseado em um [modelo](concepts-models.md) e não dá suporte a componentes:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

## <a name="digital-twin-relationship-change-notifications"></a>Notificações de alteração da relação do gêmeo digital

As **notificações de alteração da relação** são disparadas quando qualquer relação de um gêmeo digital é criada, atualizada ou excluída. 

### <a name="properties"></a>Propriedades

Aqui estão os campos no corpo de uma notificação de alteração de borda.

| Nome    | Valor |
| --- | --- |
| `id` | Identificador da notificação, como um UUID ou um contador mantido pelo serviço. `source` + `id` é exclusivo para cada evento distinto |
| `source` | Nome da instância dos Gêmeos Digitais do Azure, como *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1.0*<br>A mensagem está de acordo com esta versão da [especificação CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | ID da relação, como `<twinID>/relationships/<relationshipID>` |
| `time` | Carimbo de data/hora do momento de ocorrência da operação na relação |
| `traceparent` | Um contexto de rastreamento W3C para o evento |

### <a name="body-details"></a>Detalhes do corpo

O corpo é o conteúdo de uma relação, também no formato JSON. Ele usa o mesmo formato que uma solicitação `GET` para uma relação por meio da [API DigitalTwins](/rest/api/digital-twins/dataplane/twins). 

"Atualizar uma relação" significa que as propriedades da relação foram alteradas. 

Segue um exemplo de uma notificação de relação da atualização para atualizar uma propriedade:

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

Para `Relationship.Delete`, o corpo é igual ao da solicitação `GET` e obtém o estado mais recente antes da exclusão.

Aqui está um exemplo de uma notificação de criação ou exclusão de relacionamento:

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="next-steps"></a>Próximas etapas

Confira como criar pontos de extremidade e rotas para a entrega de eventos:
* [*Instruções: gerenciar pontos de extremidade e rotas*](how-to-manage-routes-apis-cli.md)

Ou saiba mais sobre as APIs dos Gêmeos Digitais do Azure e as opções do SDK:
* [*Instruções: usar as APIs e os SDKs dos Gêmeos Digitais do Azure*](how-to-use-apis-sdks.md)