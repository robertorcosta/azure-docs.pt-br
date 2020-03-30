---
title: Reagindo a eventos de valor-chave de configuração do aplicativo Azure
description: Use a Grade de Eventos do Azure para se inscrever em eventos de configuração de aplicativos.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523791"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagindo aos eventos de configuração do aplicativo Azure

Os eventos de configuração do aplicativo Azure permitem que os aplicativos reajam a alterações nos valores-chave. Isso é feito sem a necessidade de código complicado ou serviços de votação caros e ineficientes. Em vez disso, os eventos são empurrados através [do Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para assinantes como [Funções Azure,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para o seu próprio ouvinte http personalizado. Criticamente, você só paga pelo que usa.

Os eventos de configuração do aplicativo Azure são enviados para a Grade de Eventos do Azure, que fornece serviços de entrega confiáveis para seus aplicativos por meio de políticas de reavaliação ricas e entrega de cartas mortas. Para saber mais, consulte [a entrega e a tentativa de mensagens event grid](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Os cenários comuns de eventos de configuração do aplicativo incluem a atualização da configuração do aplicativo, o acionamento de implantações ou qualquer fluxo de trabalho orientado à configuração. Quando as alterações não forem frequentes, mas seu cenário exigir uma capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficaz.

Dê uma olhada nos [eventos de configuração do aplicativo Route Azure para um ponto final web personalizado - CLI](./howto-app-configuration-event.md) para um exemplo rápido. 

![Modelo da Grade de Eventos](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Eventos de configuração do aplicativo Azure disponíveis
A Grade de eventos usa [assinaturas de evento](../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes. As assinaturas do evento de configuração do aplicativo Azure podem incluir dois tipos de eventos:  

> |Nome do evento|Descrição|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Demitido quando um valor-chave é criado ou substituído|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Demitido quando um valor-chave é excluído|

## <a name="event-schema"></a>Esquema do evento
Os eventos de configuração do aplicativo Azure contêm todas as informações necessárias para responder às alterações em seus dados. Você pode identificar um evento de configuração de aplicativo porque a propriedade eventType começa com "Microsoft.AppConfiguration". Encontre informações adicionais sobre o uso de propriedades de evento da Grade de Eventos em [Esquema de eventos da Grade de Eventos](../event-grid/event-schema.md).  

> |Propriedade|Type|Descrição|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|ID full Azure Resource Manager da Configuração do aplicativo que emite o evento.|
> |subject|string|O URI do valor-chave que é o objeto do evento.|
> |eventTime|string|A data/hora em que o evento foi gerado, no formato ISO 8601.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" ou "Microsoft.AppConfiguration.KeyValueDeleted".|
> |ID|string|Um identificador único deste evento.|
> |dataVersion|string|A versão do esquema do objeto de dados.|
> |metadataVersion|string|A versão do esquema de propriedades de nível superior.|
> |data|objeto|Coleta de dados específicos de eventos de configuração do aplicativo Azure|
> |data.key|string|A chave do valor-chave que foi modificada ou excluída.|
> |data.label|string|O rótulo, se houver, do valor-chave que foi modificado ou excluído.|
> |data.etag|string|Para `KeyValueModified` a marca ção do novo valor-chave. Para `KeyValueDeleted` a marcação do valor-chave que foi excluído.|

Aqui está um exemplo de um evento KeyValueModified:
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

Para obter mais informações, consulte [o esquema de eventos de configuração do aplicativo Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos
Os aplicativos que lidam com eventos de configuração de aplicativos devem seguir essas práticas recomendadas:
> [!div class="checklist"]
> * Várias assinaturas podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos, portanto, não suponha que os eventos sejam de uma fonte específica. Em vez disso, verifique o tópico da mensagem para garantir que a instância de configuração do aplicativo envie o evento.
> * Verifique o eventType e não assuma que todos os eventos que você receber serão os tipos que você espera.
> * Use os campos de etag para entender se suas informações sobre objetos ainda estão atualizadas.  
> * Use os campos sequenciadores para entender a ordem dos eventos em qualquer objeto em particular.
> * Use o campo de assunto para acessar o valor-chave modificado.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a Event Grid e dê uma chance aos eventos de configuração do aplicativo Azure:

- [Sobre a Grade de Eventos](../event-grid/overview.md)
- [Eventos de configuração do aplicativo Route Azure para um ponto final da Web personalizado](./howto-app-configuration-event.md)