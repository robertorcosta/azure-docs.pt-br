---
title: Hub IoT do Azure e a Grade de Eventos | Microsoft Docs
description: Use a Grade de Eventos do Azure para acionar processos com base nas ações que ocorrem no Hub IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: a1fd99ee595c4ae91ccd06aa41fa421ca8fcc074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284545"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagir aos eventos do Hub IoT usando a Grade de Eventos para disparar ações

O Hub IoT do Azure integra-se com a Grade de Eventos do Azure para que você possa enviar notificações de eventos para outros serviços e acionar processos de downstream. Configure seus aplicativos de negócios para escutar eventos do Hub IoT para que você possa reagir a eventos críticos de maneira segura, escalonável e confiável.Por exemplo, crie um aplicativo que atualize um banco de dados, crie um ticket de trabalho e entregue uma notificação por e-mail toda vez que um novo dispositivo IoT estiver registrado no seu hub de IoT.

[Grade de Eventos do Azure](../event-grid/overview.md) é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. Grade de eventos tem suporte interno para os serviços do Azure como [Azure Functions](../azure-functions/functions-overview.md) e [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md)e pode fornecer alertas de eventos para os serviços do Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que dá suporte a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](../event-grid/overview.md).

![Arquitetura de Grade de Eventos do Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilidade regional

A integração da Grade de Eventos está disponível para os hubs IoT localizados nas regiões onde há suporte para a Grade de Eventos. Para obter a lista mais recente de regiões, consulte [Uma introdução à Grade de Eventos do Azure](../event-grid/overview.md).

## <a name="event-types"></a>Tipos de evento

Hub IoT publica os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registrado para um Hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é excluído de um Hub IoT. |
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo é conectado a um Hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é desconectado de um Hub IoT. |
| Microsoft.Devices.DeviceTelemetria | Publicado quando uma mensagem de telemetria de dispositivo é enviada para um hub de IoT |

Use o portal do Azure ou a CLI do Azure para configurar os eventos para publicar a partir de cada Hub IoT. Para um exemplo, experimente o tutorial [Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Esquema do evento

Eventos do Hub IoT contêm todas as informações que você precisa para responder às alterações no ciclo de vida do seu dispositivo. Você pode identificar um evento de Hub IoT, verificando se a propriedade eventType começa com **Microsoft.Devices**. Para obter mais informações sobre como usar propriedades de evento de Grade de Eventos, consulte o [esquema de evento da Grade de Eventos](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Esquema de dispositivo conectado

O exemplo a seguir mostra o esquema de um evento de dispositivo conectado:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Esquema de telemetria do dispositivo

A mensagem de telemetria do dispositivo deve estar em um formato JSON válido com o conteúdoType definido como **aplicativo/json** e contentEncoding definido como **UTF-8** nas propriedades do [sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens . Ambas as propriedades são insensíveis. Se a codificação de conteúdo não estiver definida, o IoT Hub gravará as mensagens no formato codificado base 64.

Você pode enriquecer eventos de telemetria de dispositivos antes de serem publicados no Event Grid selecionando o ponto final como Event Grid. Para obter mais informações, consulte [Visão geral dos enriquecimentos de mensagens](iot-hub-message-enrichments-overview.md).

O exemplo a seguir mostra o esquema de um evento de telemetria de dispositivos:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Esquema criado de dispositivo

O exemplo a seguir mostra o esquema de um evento criado de dispositivo:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Para obter uma descrição detalhada de cada propriedade, consulte o esquema de [eventos azure Event Grid para IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrar eventos

As assinaturas de eventos do IoT Hub podem filtrar eventos com base no tipo de evento, conteúdo de dados e assunto, que é o nome do dispositivo.

Event Grid permite [filtragem](../event-grid/event-filtering.md) de tipos de eventos, assuntos e conteúdo de dados. Ao criar a assinatura do Event Grid, você pode optar por se inscrever em eventos selecionados de IoT. Filtros de assunto no trabalho da Grade de Eventos com base em correspondências de **Começa com** (prefixo) e **Termina com** (sufixo). O filtro usa um operador `AND`, para que eventos com o assunto que correspondam ao prefixo e ao sufixo sejam entregues ao assinante.

O assunto de Eventos IoT usa o formato:

```json
devices/{deviceId}
```

Event Grid também permite filtrar atributos de cada evento, incluindo o conteúdo de dados. Isso permite que você escolha quais eventos são entregues com base no conteúdo da mensagem de telemetria. Consulte [filtragem avançada](../event-grid/event-filtering.md#advanced-filtering) para ver exemplos. Para filtrar o corpo da mensagem de telemetria, você deve definir o conteúdoType para **aplicativo/json** e contentEncoding para **UTF-8** nas propriedades do [sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagens . Ambas as propriedades são insensíveis.

Para eventos de não-telemetria como DeviceConnected, DeviceDisconnected, DeviceCreated e DeviceDeleted, a filtragem Event Grid pode ser usada ao criar a assinatura. Para eventos de telemetria, além da filtragem em Event Grid, os usuários também podem filtrar em gêmeos do dispositivo, propriedades de mensagem e corpo através da consulta de roteamento de mensagens. 

Quando você se inscreve em eventos de telemetria via Event Grid, o IoT Hub cria uma rota de mensagem padrão para enviar mensagens de dispositivo do tipo fonte de dados para event grid. Para obter mais informações sobre o roteamento de mensagens, consulte [roteamento de mensagens do IoT Hub](iot-hub-devguide-messages-d2c.md). Esta rota será visível no portal IoT Hub > Message Routing. Apenas uma rota para event grid é criada independentemente do número de assinaturas de EG criadas para eventos de telemetria. Então, se você precisar de várias assinaturas com filtros diferentes, você pode usar o operador OR nessas consultas na mesma rota. A criação e exclusão da rota é controlada por meio de assinatura de eventos de telemetria via Event Grid. Não é possível criar ou excluir uma rota para a Grade de Eventos usando o roteamento de mensagens do Hub IoT.

Para filtrar mensagens antes que os dados de telemetria sejam enviados, você pode atualizar sua [consulta de roteamento](iot-hub-devguide-routing-query-syntax.md). Observe que a consulta de roteamento só pode ser aplicada ao corpo da mensagem se o corpo for JSON. Você também deve definir o conteúdoType para **aplicativo/json** e contentEncoding para **UTF-8** nas propriedades do [sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagens .

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitações para eventos de conexão e desconexão de dispositivo

Para receber eventos de estado de conexão do dispositivo, um dispositivo deve fazer uma operação 'Telemetria de envio de D2C' ou uma 'Mensagem de Recebimento de C2D' com o Iot Hub. No entanto, observe que se um dispositivo estiver usando o protocolo AMQP para se conectar com o Iot Hub, é recomendável que eles façam uma operação 'C2D Receive Message' caso contrário, suas notificações de estado de conexão podem ser adiadas em poucos minutos. Se o dispositivo estiver usando o protocolo MQTT, o Hub IoT manterá o link C2D aberto. Para AMQP, você pode abrir o link C2D chamando a [API do ASYNC ,](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)para IoT Hub C# SDK ou [cliente do dispositivo para AMQP](iot-hub-amqp-support.md#device-client).

Se você está enviando telemetria, o link D2C está aberto. 

Se a conexão do dispositivo estiver piscando, o que significa que o dispositivo se conecta e se desconecta com freqüência, não enviaremos todos os estados de conexão, mas publicaremos o estado de conexão atual tomado em um instantâneo periódico, até que a cintilação continue. Receber o mesmo evento de estado de conexão com diferentes números de seqüência ou eventos diferentes do estado de conexão significa que houve uma mudança no estado de conexão do dispositivo.

## <a name="tips-for-consuming-events"></a>Dicas para o consumo de eventos

Aplicativos que tratam os eventos de Hub IoT devem seguir essas práticas sugeridas:

* Várias assinaturas podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos, então não assuma que os eventos são de uma fonte específica. Sempre verifique o tópico de mensagem para certificar-se de que eles vêm do hub IoT que você espera.

* Não presuma que todos os eventos que você recebe são os tipos que você espera. Sempre verifique o eventType antes de processar a mensagem.

* As mensagens podem chegar fora de ordem ou após um atraso. Use o campo etag para entender se suas informações sobre objetos estão atualizadas para eventos criados por dispositivos ou dispositivos excluídos.

## <a name="next-steps"></a>Próximas etapas

* [Tente o tutorial de eventos do Hub IoT](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Saiba como ordenar os eventos de conexão e desconexão de dispositivo](iot-hub-how-to-order-connection-state-events.md)

* [Saiba mais sobre a Grade de Eventos](../event-grid/overview.md)

* [Comparar as diferenças entre roteamento de eventos e mensagens do Hub IoT](iot-hub-event-grid-routing-comparison.md)

* [Aprenda a usar eventos de telemetria de IoT para implementar análiseespacial de IoT usando o Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
