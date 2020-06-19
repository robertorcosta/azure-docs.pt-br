---
title: Fila de armazenamento como um manipulador de eventos para os eventos da Grade de Eventos do Azure
description: Descreve como você pode usar as filas de armazenamento do Azure como manipuladores de eventos para os eventos da Grade de Eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: f62f2b5bc01518af29bd1deb17a38e9fe105a4ed
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800564"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Fila de armazenamento como um manipulador de eventos para os eventos da Grade de Eventos do Azure
Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos e o **Armazenamento de Filas do Azure** é um deles. 

Use o **Armazenamento de Filas** para receber eventos que precisam efetuar pull. Você pode usar o Armazenamento de Filas quando tem um processo de execução longa que demora muito para responder. Ao enviar eventos para o Armazenamento de Filas, o aplicativo pode receber e processar os eventos de acordo com a própria agenda.

## <a name="tutorials"></a>Tutoriais
Consulte o tutorial a seguir para ter um exemplo de uso do Armazenamento de filas como um manipulador de eventos. 

|Title  |Descrição  |
|---------|---------|
| [Início Rápido: encaminhar eventos personalizados para o Armazenamento de Filas do Azure com a CLI do Azure e a Grade de Eventos](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um Armazenamento de filas. |

## <a name="rest-examples-for-put"></a>Exemplos de REST (para PUT)

### <a name="storage-queue-as-the-event-handler"></a>Fila de armazenamento como manipulador de eventos

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                "queueName": "<QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-the-event-handler---delivery-with-managed-identity"></a>Fila de armazenamento como o manipulador de eventos – entrega com identidade gerenciada

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "StorageQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                    "queueName": "<QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination"></a>Fila de armazenamento como um destino de mensagens mortas

```json
{
    "name": "",
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterDestination": 
        {
            "endpointType": "StorageBlob",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                "blobContainerName": "test"
            }
        }
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination---managed-identity"></a>Fila de armazenamento como um destino de mensagens mortas – identidade gerenciada

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "deadLetterDestination": 
            {
                "endpointType": "StorageBlob",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                    "blobContainerName": "test"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 
