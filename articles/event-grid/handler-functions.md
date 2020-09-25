---
title: Função do Azure como um manipulador de eventos para os eventos da Grade de Eventos do Azure
description: Descreve como você pode usar as funções do Azure como manipuladores de eventos para os eventos da Grade de Eventos.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 87aeb78729dcc7bec9f193fab389e5c0952e63d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270297"
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
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Habilitar o envio em lote
Para obter uma taxa de transferência mais alta, habilite o envio em lote na assinatura. Se você estiver usando o portal do Azure, poderá definir o máximo de eventos por lote e o tamanho de lote preferencial em quilobytes bytes no momento da criação de uma assinatura ou após a criação. 

Você pode definir configurações de lote usando o modelo portal do Azure, PowerShell, CLI ou Resource Manager. 

### <a name="azure-portal"></a>Portal do Azure
No momento da criação de uma assinatura na interface do usuário, na página **criar assinatura de evento** , alterne para a guia **recursos avançados** e defina valores para **máximo de eventos por lote** e **tamanho de lote preferencial em kilobytes**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Habilitar o envio em lote no momento da criação de uma assinatura":::

Você pode atualizar esses valores para uma assinatura existente na guia **recursos** da página de **Tópicos da grade de eventos** . 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Habilitar o envio em lote após a criação":::

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Você pode definir **maxEventsPerBatch** e **preferredBatchSizeInKilobytes** em um modelo de Azure Resource Manager. Para obter mais informações, consulte [referência de modelo do Microsoft. EventGrid eventSubscriptions](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>CLI do Azure
Você pode usar o comando [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) ou [AZ eventgrid Event-Subscription Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) para definir configurações relacionadas ao lote usando os seguintes parâmetros: `--max-events-per-batch` ou `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
Você pode usar o cmdlet [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) ou [Update-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) para definir configurações relacionadas ao lote usando os seguintes parâmetros: `-MaxEventsPerBatch` ou `-PreferredBatchSizeInKiloBytes` .

> [!NOTE]
> Não há suporte para a entrega de eventos a uma função do Azure em **outro locatário** . 

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 
