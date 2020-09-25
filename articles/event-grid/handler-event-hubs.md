---
title: Hub de eventos como um manipulador de eventos para os eventos da Grade de Eventos do Azure
description: Descreve como você pode usar os hubs de eventos como manipuladores de eventos para os eventos da Grade de Eventos do Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 5e6a84c1737c6b8a575f47576aeb1d3d9efae6eb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322556"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Hub de eventos como um manipulador de eventos para os eventos da Grade de Eventos do Azure
Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa uma ação para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos, e o **Hubs de Eventos do Azure** é um deles. 

Use o **Hubs de Eventos** quando sua solução receber eventos da Grade de Eventos mais rápido do que é capaz de processar. Quando os eventos estiverem em um hub de eventos, seu aplicativo poderá processar a partir do hub de eventos em seu próprio agendamento. Você pode dimensionar o processamento dos eventos para manipular os eventos de entrada.

## <a name="tutorials"></a>Tutoriais
Veja os exemplos a seguir: 

|Title  |Descrição  |
|---------|---------|
| [Início Rápido: Encaminhar eventos personalizados para os Hubs de Eventos do Azure com a CLI do Azure](custom-event-to-eventhub.md) | Envia um evento personalizado para um hub de eventos para processamento por um aplicativo. |
| [Modelo do Resource Manager: Criar um tópico personalizado de Grade de Eventos e enviar eventos para um hub de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo do Gerenciador de Recursos que cria uma assinatura para um tópico personalizado. Envia eventos para os Hubs de Eventos do Azure. |

## <a name="message-properties"></a>Propriedades da mensagem
Se você usar um **Hub de eventos** como um manipulador de eventos para eventos da grade de eventos, essas são as propriedades que você recebe nos cabeçalhos de mensagem: 

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| aeg-subscription-name | Nome da assinatura de evento. |
| aeg-delivery-count | <p>Número de tentativas feitas para o evento.</p> <p>Exemplo: "1"</p> |
| aeg-event-type | <p>Tipo do evento.</p><p> Exemplo: "Microsoft.Storage.blobCreated"</p> | 
| aeg-metadata-version | <p>A versão de metadados do evento.</p> <p>Exemplo: "1".</p><p> Para o **esquema de evento da Grade de Eventos**, essa propriedade representa a versão de metadados e, para o **esquema de evento da nuvem**, ela representa a **versão de especificação**. </p>|
| aeg-data-version | <p>Versão de dados do evento.</p><p>Exemplo: "1".</p><p>Para o **esquema de evento da Grade de Eventos**, essa propriedade representa a versão dos dados e para o **esquema de evento da nuvem**, ela não se aplica.</p> |
| aeg-output-event-id | ID do evento da Grade de Eventos. |

## <a name="rest-examples-for-put"></a>Exemplos de REST (para PUT)


### <a name="event-hub"></a>Hub de Eventos

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Hub de eventos – entrega com identidade gerenciada

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

> [!NOTE]
> Não há suporte para a entrega de eventos a um hub de eventos do Azure em **outro locatário** . 

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 
