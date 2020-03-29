---
title: Publicar, assinar eventos em nuvem - Azure Event Grid IoT Edge | Microsoft Docs
description: Publicar, assinar eventos na nuvem usando webhook com Event Grid no IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844575"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Tutorial: Publique, assine eventos na nuvem

Este artigo percorre todas as etapas necessárias para publicar e assinar eventos usando Event Grid no IoT Edge. Este tutorial usa e a função Azure como manipuladora de eventos. Para tipos adicionais de destino, consulte [manipuladores de eventos](event-handlers.md).

Consulte [Event Grid Concepts](concepts.md) para entender o que é um tópico e assinatura da grade de eventos antes de prosseguir.

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, você precisará:

* **Assinatura do Azure** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no início rápido para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Crie uma função Azure no portal Azure

Siga os passos descritos no [tutorial](../../azure-functions/functions-create-first-azure-function.md) para criar uma função Azure. 

Substitua o trecho de código pelo seguinte código:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

Na nova função, **selecione Obter url de função** no canto superior direito, selecione padrão **(chave de função)** e, em seguida, selecione **Copiar**. Você usará o valor de URL da função mais tarde no tutorial.

> [!NOTE]
> Consulte a documentação funções do [Azure](../../azure-functions/functions-overview.md) para obter mais amostras e tutoriais sobre como reagir a eventos usando gatilhos de eventos EventGrid.

## <a name="create-a-topic"></a>Criar um tópico

Como editor de um evento, você precisa criar um tópico da grade de eventos. Tópico refere-se a um ponto final para onde os editores podem enviar eventos.

1. Crie topic2.json com o seguinte conteúdo. Consulte nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Execute o seguinte comando para criar o tópico. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Execute o seguinte comando para verificar se o tópico foi criado com sucesso. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Saída de exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Criar uma assinatura de evento

Os assinantes podem se inscrever para eventos publicados em um tópico. Para receber qualquer evento, os assinantes precisarão criar uma assinatura da grade de Eventos em um tópico de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie a subscription2.json com o seguinte conteúdo. Consulte nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > O **endpointType** especifica que o assinante é um Webhook.  O **endpointUrl** especifica a URL na qual o assinante está ouvindo eventos. Esta URL corresponde à amostra de função Azure que você configura anteriormente.
2. Execute o seguinte comando para criar a assinatura. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Execute o seguinte comando para verificar se a assinatura foi criada com sucesso. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicar um evento

1. Crie event2.json com o seguinte conteúdo. Consulte nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. Execute o seguinte comando para publicar o evento

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificar a entrega do evento

Você pode ver o evento entregue no portal Azure a opção **Monitor** de sua função.

## <a name="cleanup-resources"></a>Recursos de limpeza

* Execute o seguinte comando para excluir o tópico e todas as suas assinaturas

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Exclua a função Azure criada no portal Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um tópico de grade de eventos, assinatura e eventos publicados. Agora que você sabe os passos básicos, veja os seguintes artigos:

* Para solucionar problemas com o uso do Azure Event Grid no IoT Edge, consulte [Guia de solução de problemas](troubleshoot.md).
* Criar/atualizar a assinatura com [filtros](advanced-filtering.md).
* Configure a persistência do módulo Event Grid no [linux](persist-state-linux.md) ou [Windows](persist-state-windows.md)
* Siga a [documentação](configure-client-auth.md) para configurar a autenticação do cliente
* Encaminhe eventos para a Azure Event Grid na nuvem seguindo este [tutorial](forward-events-event-grid-cloud.md)
* [Monitore tópicos e assinaturas no limite](monitor-topics-subscriptions.md)
