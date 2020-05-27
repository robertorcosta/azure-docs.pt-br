---
title: Hub de eventos como um manipulador de eventos para os eventos da Grade de Eventos do Azure
description: Descreve como você pode usar os hubs de eventos como manipuladores de eventos para os eventos da Grade de Eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: c9ce4e7da51005dcb06c9df420d80f4d2c7b93e9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596015"
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
Se você usar um **hub de eventos** como um manipulador de eventos para eventos da Grade de Eventos, defina os seguintes cabeçalhos de mensagem: 

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| aeg-subscription-name | Nome da assinatura de evento. |
| aeg-delivery-count | <p>Número de tentativas feitas para o evento.</p> <p>Exemplo: "1"</p> |
| aeg-event-type | <p>Tipo do evento.</p><p> Exemplo: "Microsoft.Storage.blobCreated"</p> | 
| aeg-metadata-version | <p>A versão de metadados do evento.</p> <p>Exemplo: "1".</p><p> Para o **esquema de evento da Grade de Eventos**, essa propriedade representa a versão de metadados e, para o **esquema de evento da nuvem**, ela representa a **versão de especificação**. </p>|
| aeg-data-version | <p>Versão de dados do evento.</p><p>Exemplo: "1".</p><p>Para o **esquema de evento da Grade de Eventos**, essa propriedade representa a versão dos dados e para o **esquema de evento da nuvem**, ela não se aplica.</p> |
| aeg-output-event-id | ID do evento da Grade de Eventos. |


## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 
