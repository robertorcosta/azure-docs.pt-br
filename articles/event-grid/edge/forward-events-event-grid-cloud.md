---
title: Eventos de borda dianteira para a nuvem event grid - Azure Event Grid IoT Edge | Microsoft Docs
description: Eventos de borda avançada para a nuvem event grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844710"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Tutorial: Encaminhe eventos para a nuvem da Event Grid

Este artigo percorre todos os passos necessários para encaminhar eventos de borda para event grid na nuvem DoZure. Você pode querer fazê-lo pelas seguintes razões:

* Reaja aos eventos de borda na nuvem.
* Encaminhe eventos para event grid na nuvem e use hubs de eventos do Azure ou filas de armazenamento Do Zure para buffer de eventos antes de processá-los na nuvem.

 Para completar este tutorial, você precisa ter uma compreensão dos conceitos da Event Grid no [edge](concepts.md) e [do Azure](../concepts.md). Para tipos adicionais de destino, consulte [manipuladores de eventos](event-handlers.md). 

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, você precisará:

* **Assinatura do Azure** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no início rápido para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Crie tópico e assinatura da grade de eventos em nuvem

Crie um tópico de grade de eventos e assinatura na nuvem seguindo [este tutorial](../custom-event-quickstart-portal.md). Anote `topicURL` `sasKey`para `topicName` baixo , e do tópico recém-criado que você usará mais tarde no tutorial.

Por exemplo, se você criou `testegcloudtopic` um tópico chamado no Oeste dos EUA, os valores se pareceriam com:

* **TopicUrl**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TópicoNome**:`testegcloudtopic`
* **SasKey**: Disponível em **AccessKey** do seu tópico. Use **a chave1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Criar tópico de grade de eventos na borda

1. Crie topic3.json com o seguinte conteúdo. Consulte nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Execute o seguinte comando para criar o tópico. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Execute o seguinte comando para verificar se o tópico foi criado com sucesso. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Saída de exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Criar assinatura do Event Grid no limite

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie a subscription3.json com o seguinte conteúdo. Consulte nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > O **endpointUrl** especifica que a URL do tópico Event Grid na nuvem. O **sasKey** refere-se à chave do tópico de nuvem event grid. O valor no **topicName** será usado para carimbar todos os eventos de saída para Event Grid. Isso pode ser útil ao postar em um tópico de domínio da Event Grid. Para obter mais informações sobre o tópico de domínio event grid, consulte [domínios de eventos](../event-domains.md)

    Por exemplo,
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Execute o seguinte comando para criar a assinatura. HTTP Status Code of 200 OK deve ser devolvido.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Execute o seguinte comando para verificar se a assinatura foi criada com sucesso. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Publique um evento na borda

1. Crie event3.json com o seguinte conteúdo. Consulte [a documentação da API](api.md) para obter detalhes sobre a carga útil.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Execute o comando a seguir:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Verificar evento de borda na nuvem

Para obter informações sobre como visualizar eventos entregues pelo tópico da nuvem, consulte o [tutorial](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Recursos de limpeza

* Execute o seguinte comando para excluir o tópico e todas as suas assinaturas

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Exclua tópicos e assinaturas criados na nuvem (Azure Event Grid) também.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você publicou um evento na borda e encaminhou para event grid na nuvem Do Zure. Agora que você sabe os passos básicos para avançar para event grid na nuvem:

* Para solucionar problemas com o uso do Azure Event Grid no IoT Edge, consulte [Guia de solução de problemas](troubleshoot.md).
* Encaminhe eventos para o IoTHub seguindo este [tutorial](forward-events-iothub.md)
* Encaminhe eventos para webhook na nuvem seguindo este [tutorial](pub-sub-events-webhook-cloud.md)
* [Monitore tópicos e assinaturas no limite](monitor-topics-subscriptions.md)
