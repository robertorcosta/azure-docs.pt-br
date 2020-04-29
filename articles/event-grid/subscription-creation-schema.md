---
title: Esquema de assinatura de Grade de Eventos do Azure
description: Este artigo descreve as propriedades de assinatura de um evento com a grade de eventos do Azure. Esquema de assinatura da grade de eventos.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720751"
---
# <a name="event-grid-subscription-schema"></a>Esquema de assinatura de Grade de Eventos

Para criar uma assinatura de grade de eventos, você envia uma solicitação para a operação de assinatura Create Event. Use o seguinte formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Por exemplo, para criar uma inscrição de evento para uma conta de armazenamento denominada `examplestorage` em um grupo de recursos denominado `examplegroup`, use o seguinte formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

O nome da assinatura de evento deve ter 3 a 64 caracteres de comprimento e só pode conter a-z, A-Z, 0 a 9, e "-". O artigo descreve as propriedades e o esquema para o corpo da solicitação.
 
## <a name="event-subscription-properties"></a>Propriedades da assinatura do evento

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| destino | objeto | O objeto que define o ponto de extremidade. |
| filtro | objeto | Um campo opcional para filtrar os tipos de eventos. |

### <a name="destination-object"></a>objeto de destino

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| endpointType | cadeia de caracteres | O tipo de ponto de extremidade para a assinatura (webhook/HTTP, o Hub de evento ou fila). | 
| endpointUrl | cadeia de caracteres | A URL de destino para eventos nesta assinatura de evento. | 

### <a name="filter-object"></a>objeto filter

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| includedEventTypes | matriz | Correspondência quando o tipo de evento na mensagem de evento é uma correspondência exata para esses nomes de tipo de evento. Gera um erro quando o nome do evento não coincide com os nomes de tipo de evento registrados para a origem do evento. O padrão corresponde a todos os tipos de evento. |
| subjectBeginsWith | cadeia de caracteres | Uma correspondência de prefixo de filtro para o campo de assunto no evento mensagem. A cadeia de caracteres padrão ou vazia corresponde a tudo. | 
| subjectEndsWith | cadeia de caracteres | Uma correspondência de sufixo de filtro para o campo de assunto no evento mensagem. A cadeia de caracteres padrão ou vazia corresponde a tudo. |
| isSubjectCaseSensitive | cadeia de caracteres | Controla a correspondência que diferencia maiúsculas e minúsculas para filtros. |


## <a name="example-subscription-schema"></a>Esquema de assinatura de exemplo

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [O que é uma Grade de eventos?](overview.md)
