---
title: Conexão híbrida de retransmissão como um manipulador de eventos para eventos da Grade de Eventos do Azure
description: Descreve como você pode usar as conexões híbridas da Retransmissão do Azure como manipuladores de eventos para os eventos da Grade de Eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 04fbc7b739fa8ea7b08a3341c2f78244c445e721
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800876"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Conexão híbrida de retransmissão como um manipulador de eventos para eventos da Grade de Eventos do Azure
Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos, e a **Retransmissão do Azure** é um deles. 

Use as **Conexões Híbridas de Retransmissão** do Azure para enviar eventos para aplicativos que estão em uma rede corporativa e não têm um ponto de extremidade publicamente acessível.

## <a name="tutorials"></a>Tutoriais
Consulte o tutorial a seguir para ter um exemplo de uso da conexão híbrida da Retransmissão do Azure como um manipulador de eventos. 

|Title  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para conexão híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento por um aplicativo de escuta. |

## <a name="rest-example-for-put"></a>Exemplo de REST (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 