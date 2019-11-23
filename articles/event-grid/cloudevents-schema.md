---
title: Usar a Grade de Eventos do Azure com eventos no esquema CloudEvents
description: Descreve como configurar o esquema de CloudEvents para eventos na Grade de Eventos do Azure.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 6a0e24ce7fa11c6373fbaada40cd9f1b1e7f55a2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325466"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Use CloudEvents v1.0 schema with Event Grid

In addition to its [default event schema](event-schema.md), Azure Event Grid natively supports events in the [JSON implementation of CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) and [HTTP protocol binding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever dados de eventos.

O CloudEvents simplifica a interoperabilidade, fornecendo um esquema comum do evento para publicar e consumir eventos com base em nuvem. Esse esquema permite ferramentas uniforme, formas padrão de roteamento e manipulação de eventos e maneiras universais de desserializar o esquema de evento externo. Com um esquema comum, você pode integrar facilmente mais trabalho entre plataformas.

O CloudEvents está sendo criado por vários [colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, por meio da [Cloud Native Computing Foundation](https://www.cncf.io/). It's currently available as version 1.0.

Este artigo descreve como usar o esquema de CloudEvents com a Grade de Eventos.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalar versão prévia do recurso

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Esquema de CloudEvent

Aqui está um exemplo de um evento de Armazenamento de Blob do Azure no formato CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

A detailed description of the available fields, their types, and definitions in CloudEvents v0.1 is [available here](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Os valores dos cabeçalhos para eventos entregues no esquema CloudEvents e no esquema da Grade de Eventos são os mesmos, exceto para `content-type`. Para o esquema CloudEvents, esse valor de cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"`. Para o esquema Grade de Eventos, esse valor de cabeçalho é `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Configurar a Grade de Eventos para CloudEvents

Você pode usar a Grade de Eventos para entrada e saída de eventos no esquema CloudEvents. Você pode usar o CloudEvents para eventos do sistema, como eventos do Armazenamento de Blob e eventos do Hub IoT e eventos personalizados. Também pode transformar esses eventos durante a transmissão para trás e para frente.


| Esquema de entrada       | Esquema de saída
|--------------------|---------------------
| Formato de CloudEvents | Formato de CloudEvents
| Formato da Grade de Eventos  | Formato de CloudEvents
| Formato de CloudEvents | Formato da Grade de Eventos
| Formato da Grade de Eventos  | Formato da Grade de Eventos

Para todos os esquemas de evento, a Grade de Eventos requer validação ao publicar em um tópico de grade de eventos e ao criar uma inscrição de evento. Para saber mais, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).

### <a name="input-schema"></a>Esquema de entrada

Quando você cria o tópico personalizado, você definir o esquema de entrada para um tópico personalizado.

Para a CLI do Azure, use:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Para o PowerShell, use:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

A versão atual do CloudEvents não dá suporte a envio em lote de eventos. Para publicar eventos com o esquema do CloudEvent para um tópico, publique cada evento individualmente.

### <a name="output-schema"></a>Esquema de saída

Quando você cria a assinatura de evento, você definir o esquema de saída.

Para a CLI do Azure, use:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Para o PowerShell, use:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 No momento, você não pode usar um gatilho de Grade de Eventos para um aplicativo do Azure Functions, quando o evento é entregue no esquema do CloudEvents. Use um gatilho HTTP. Para obter exemplos de implementação de um gatilho HTTP que recebe eventos no esquema CloudEvents, consulte [Use um gatilho HTTP como um gatilho de Grade de Eventos](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Endpoint Validation with CloudEvents v1.0

If you are already familiar with Event Grid, you may be aware of Event Grid's endpoint validation handshake for preventing abuse. CloudEvents v1.0 implements its own [abuse protection semantics](security-authentication.md#webhook-event-delivery) using the HTTP OPTIONS method. Saiba mais sobre isso [aqui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). When using the CloudEvents schema for output, Event Grid uses with the CloudEvents v1.0 abuse protection in place of the Event Grid validation event mechanism.

## <a name="next-steps"></a>Próximos passos

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Incentivamos você a testar, comentar e [contribuir](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) para CloudEvents.
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
