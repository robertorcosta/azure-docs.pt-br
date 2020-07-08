---
title: Função do Azure como um manipulador de eventos para os eventos da Grade de Eventos do Azure
description: Descreve como você pode usar as funções do Azure como manipuladores de eventos para os eventos da Grade de Eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0152a9fc11562744f83ab9d20466a3dcc8e2e6e0
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800423"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Função do Azure como um manipulador de eventos para os eventos da Grade de Eventos

Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa uma ação para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos e o **Azure Functions** é um deles. 

Use o **Azure Functions** em uma arquitetura sem servidor para responder a eventos da Grade de Eventos. Ao usar a função do Azure como o manipulador, use o gatilho da Grade de Eventos em vez de um gatilho HTTP genérico. A Grade de Eventos valida automaticamente os próprios gatilhos. Com os gatilhos HTTP genéricos, você deve implementar a [resposta de validação](webhook-event-delivery.md).

Para obter mais informações, consulte [Gatilho da Grade de Eventos para o Azure Functions](../azure-functions/functions-bindings-event-grid.md) para uma visão geral do uso do gatilho da Grade de Eventos nas funções.

## <a name="tutorials"></a>Tutoriais

|Title  |Descrição  |
|---------|---------|
| [Início Rápido: Manipular eventos com função](custom-event-to-function.md) | Envia um evento personalizado para uma função para processamento. |
| [Tutorial: automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos](resize-images-on-storage-blob-upload-event.md) | Os usuários fazem o upload de imagens por meio do aplicativo Web para a conta de armazenamento. Quando um blob de armazenamento é criado, a Grade de Eventos envia um evento para o aplicativo de função, que redimensiona a imagem carregada. |
| [Tutorial: transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md) | Quando os Hubs de Eventos criam um arquivo de Captura, a Grade de Eventos envia um evento para um aplicativo de função. O aplicativo recupera o arquivo de Captura e migra dados para um data warehouse. |
| [Tutorial: Exemplos de integração do Barramento de Serviço do Azure para a Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para um aplicativo de funções e um aplicativo lógico. |

## <a name="rest-example-for-put"></a>Exemplo REST (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 
