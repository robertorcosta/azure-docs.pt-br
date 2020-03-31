---
title: Manipuladores de eventos e destinos - Azure Event Grid IoT Edge | Microsoft Docs
description: Manipuladores de eventos e destinos em Event Grid on Edge
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849741"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Manipuladores de eventos e destinos em Event Grid on Edge

Um manipulador de eventos é o local onde o evento para mais ações ou para processar o evento. Com o módulo Event Grid on Edge, o manipulador de eventos pode estar no mesmo dispositivo de borda, outro dispositivo ou na nuvem. Você pode usar qualquer WebHook para lidar com eventos ou enviar eventos para um dos manipuladores nativos, como o Azure Event Grid.

Este artigo fornece informações sobre como configurar cada um.

## <a name="webhook"></a>WebHook

Para publicar em um ponto final `endpointType` `WebHook` do WebHook, defina o para e forneça:

* endpointUrl: A URL do ponto final do WebHook

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Grade de Eventos do Azure

Para publicar em um ponto final de nuvem `endpointType` `eventGrid` do Azure Event Grid, defina o e forneça:

* endpointUrl: URL tópico da grade de eventos na nuvem
* sasKey: Chave SAS do Event Grid Topic
* tópicoNome: Nome para carimbar todos os eventos de saída para Event Grid. O nome do tópico é útil ao postar em um tópico de Domínio da Grade de Eventos.

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

## <a name="iot-edge-hub"></a>Hub de borda IoT

Para publicar em um módulo `endpointType` do `edgeHub` Edge Hub, defina o e forneça:

* saídaNome: A saída na qual o módulo Event Grid irá encaminhar eventos que correspondem a esta assinatura ao edgeHub. Por exemplo, os eventos que correspondem à assinatura abaixo serão gravados para /messages/modules/eventgridmodule/outputs/sampleSub4.

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

## <a name="event-hubs"></a>Hubs de Eventos

Para publicar em um Event `endpointType` `eventHub` Hub, defina o e forneça:

* conexãoString: seqüência de conexão para o Hub de eventos específico que você está direcionando gerado por meio de uma Diretiva de Acesso Compartilhado.

    >[!NOTE]
    > A seqüência de conexões deve ser específica da entidade. Usar uma seqüência de conexão namespace não funcionará. Você pode gerar uma seqüência de conexão específica da entidade navegando para o Centro de Eventos específico que você gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova seqüência de connecção específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Filas de barramento de serviço

Para publicar em uma fila de `endpointType` `serviceBusQueue` ônibus de serviço, defina o e forneça:

* conexãoString: seqüência de conexão para a fila de barramento de serviço específica que você está direcionando gerada através de uma Diretiva de Acesso Compartilhado.

    >[!NOTE]
    > A seqüência de conexões deve ser específica da entidade. Usar uma seqüência de conexão namespace não funcionará. Gere uma seqüência de conexão específica da entidade navegando para a fila de ônibus de serviço específica que você gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova seqüência de connecção específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Tópicos do Service Bus

Para publicar em um tópico `endpointType` de `serviceBusTopic` barramento de serviço, defina o e forneça:

* conexãoString: seqüência de conexão para o tópico específico do barramento de serviço que você está direcionando gerado por meio de uma diretiva de acesso compartilhado.

    >[!NOTE]
    > A seqüência de conexões deve ser específica da entidade. Usar uma seqüência de conexão namespace não funcionará. Gere uma seqüência de conexão específica da entidade navegando para o tópico específico de barramento de serviço que você gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova seqüência de connecção específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Filas de Armazenamento

Para publicar em uma fila `endpointType` de `storageQueue` armazenamento, defina o e forneça:

* queueNome: Nome da fila de armazenamento para a que você está publicando.
* conexãoString: string de conexão para a conta de armazenamento em que a fila de armazenamento está.

    >[!NOTE]
    > Hubs de eventos desalinhados, filas de ônibus de serviço e tópicos de barramento de serviço, a seqüência de conexão usada para filas de armazenamento não é específica da entidade. Em vez disso, deve apenas a seqüência de conexão para a conta de armazenamento.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```