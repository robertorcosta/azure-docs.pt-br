---
title: Eventos de Frente de Eventos grid para IoTHub - Azure Event Grid IoT Edge | Microsoft Docs
description: Eventos forward event grid para IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844693"
---
# <a name="tutorial-forward-events-to-iothub"></a>Tutorial: Avance eventos para o IoTHub

Este artigo percorre todos os passos necessários para encaminhar eventos do Event Grid para outros módulos de IoT Edge, IoTHub usando rotas. Você pode querer fazê-lo pelas seguintes razões:

* Continue a usar quaisquer investimentos já existentes com o roteamento do EdgeHub
* Prefira encaminhar todos os eventos de um dispositivo apenas via IoT Hub

Para completar este tutorial, você precisa entender os seguintes conceitos:

- [Conceitos da grade de eventos](concepts.md)
- [Hub do IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, você precisará:

* **Assinatura do Azure** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no início rápido para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Criar tópico

Como editor de um evento, você precisa criar um tópico da grade de eventos. O tópico refere-se a um ponto final para onde os editores podem enviar eventos para.

1. Crie topic4.json com o seguinte conteúdo. Consulte nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Execute o seguinte comando para criar o tópico. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Execute o seguinte comando para verificar se o tópico foi criado com sucesso. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Saída de exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Criar assinatura de evento

Os assinantes podem se inscrever para eventos publicados em um tópico. Para receber qualquer evento, eles precisarão criar uma assinatura da grade de eventos em um tópico de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie a assinatura4.json com o conteúdo abaixo. Consulte nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > O `endpointType` especifica que o `edgeHub`assinante é . O `outputName` especifica a saída na qual o módulo Event Grid irá encaminhar eventos que correspondem a esta assinatura ao edgeHub. Por exemplo, os eventos que correspondem `/messages/modules/eventgridmodule/outputs/sampleSub4`à assinatura acima serão gravados para .
2. Execute o seguinte comando para criar a assinatura. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Execute o seguinte comando para verificar se a assinatura foi criada com sucesso. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Configure uma rota de hub de borda

Atualize a rota do hub de borda para encaminhar os eventos de assinatura de eventos a serem encaminhados ao IoTHub da seguinte forma:

1. Entre no [portal do Azure](https://ms.portal.azure.com)
1. Navegue até o **IoT Hub**.
1. Selecione **IoT Edge** no menu
1. Selecione o ID do dispositivo de destino na lista de dispositivos.
1. Selecione **Módulos de conjunto**.
1. Selecione **Next** e para a seção rotas.
1. Nas rotas, adicione uma nova rota

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Por exemplo,

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > A rota acima encaminhará todos os eventos combinados para que esta assinatura seja encaminhada para o hub IoT. Você pode usar os recursos [de roteamento](../../iot-edge/module-composition.md) do hub edge para filtrar ainda mais e encaminhar os eventos da Grade de Eventos para outros módulos de Borda IoT.

## <a name="setup-iot-hub-route"></a>Rota do Hub IoT de configuração

Consulte o tutorial de [roteamento do IoT Hub](../../iot-hub/tutorial-routing.md) para configurar uma rota a partir do hub IoT para que você possa visualizar eventos encaminhados a partir do módulo Event Grid. Use `true` para a consulta para manter o tutorial simples.  



## <a name="publish-an-event"></a>Publicar um evento

1. Crie event4.json com o seguinte conteúdo. Consulte nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

    ```json
        [
          {
            "id": "eventId-iothub-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. Execute o seguinte comando para publicar o evento:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificar a entrega do evento

Consulte o tutorial [de roteamento](../../iot-hub/tutorial-routing.md) do IoT Hub para ver os eventos.

## <a name="cleanup-resources"></a>Recursos de limpeza

* Execute o seguinte comando para excluir o tópico e todas as suas assinaturas na borda:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Exclua todos os recursos criados durante a configuração do roteamento do IoTHub na nuvem também.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um tópico de grade de eventos, assinatura do edge hub e eventos publicados. Agora que você sabe os passos básicos para avançar para um hub de borda, veja os seguintes artigos:

* Para solucionar problemas com o uso do Azure Event Grid no IoT Edge, consulte [Guia de solução de problemas](troubleshoot.md).
* Use filtros de rota [do hub de borda](../../iot-edge/module-composition.md) para eventos de partição
* Configure a persistência do módulo Event Grid no [linux](persist-state-linux.md) ou [Windows](persist-state-windows.md)
* Siga a [documentação](configure-client-auth.md) para configurar a autenticação do cliente
* Encaminhe eventos para a Azure Event Grid na nuvem seguindo este [tutorial](forward-events-event-grid-cloud.md)
* [Monitore tópicos e assinaturas no limite](monitor-topics-subscriptions.md)