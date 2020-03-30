---
title: Esquema de configuração do aplicativo Azure Event Grid Azure
description: Descreve as propriedades fornecidas para eventos de configuração do aplicativo Azure com o Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66735775"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Esquema de eventos do Azure Event Grid para configuração do aplicativo Azure

Este artigo fornece as propriedades e o esquema para eventos de configuração do aplicativo Azure. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

Para obter uma lista de scripts de exemplo e tutoriais, consulte [a fonte do evento Azure App Configuration](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Tipos de evento disponíveis

A configuração do aplicativo Azure emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Aumentado quando um valor-chave é criado ou substituído. |
| Microsoft.AppConfiguration.KeyValueExcluído | Aumentado quando um valor-chave é excluído. |

## <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de um evento modificado de valor-chave: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

O esquema para um evento excluído de valor-chave é semelhante: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | string | Identificador exclusivo do evento. |
| data | objeto | Dados do evento configuração do aplicativo. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| chave | string | A chave do valor-chave que foi modificada ou excluída. |
| label | string | O rótulo, se houver, do valor-chave que foi modificado ou excluído. |
| etag | string | Para `KeyValueModified` a marca ção do novo valor-chave. Para `KeyValueDeleted` a marcação do valor-chave que foi excluído. |
 
## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para obter uma introdução ao trabalho com eventos de configuração do aplicativo Azure, consulte [eventos de configuração do aplicativo Route Azure - Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 