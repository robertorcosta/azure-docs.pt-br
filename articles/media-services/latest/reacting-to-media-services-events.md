---
title: Reagir aos eventos dos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo descreve como usar a grade de eventos do Azure para assinar eventos de serviços de mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: e24bacb0ea7ab406442022915872fc77e9cc1a5e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74887877"
---
# <a name="handling-event-grid-events"></a>Lidando com os eventos da Grade de Eventos

Os eventos dos Serviços de Mídia permitem aos aplicativos reagir a diferentes eventos (por exemplo, o evento de alteração de estado do trabalho) usando arquiteturas sem servidor modernas. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes. Em vez disso, os eventos são enviados por push pela [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) aos manipuladores de eventos como [Azure Functions](https://azure.microsoft.com/services/functions/), [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/), ou até mesmo seu próprio Webhook, e você só paga pelo que usa. Para saber mais sobre preços, confira [Preços da Grade de Eventos](https://azure.microsoft.com/pricing/details/event-grid/).

A disponibilidade para eventos dos Serviços de Mídia está vinculada à [disponibilidade](../../event-grid/overview.md) da Grade de Eventos e estará disponível em outras regiões, conforme a Grade de Eventos também for disponibilizada.  

## <a name="media-services-events-and-schemas"></a>Esquemas e eventos de serviços de mídia

A Grade de eventos usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes. Os eventos dos Serviços de Mídia contêm todas as informações que você precisa para responder às alterações em seus dados. Você pode identificar um evento de Serviços de Mídia porque a propriedade eventType começa com "Microsoft.Media".

Para obter mais informações, confira [Esquemas de eventos dos Serviços de Mídia](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos

Aplicativos que manipulam eventos de Serviços de Mídia devem seguir algumas práticas recomendadas:

* Como várias assinaturas podem ser configuradas para eventos de rota para o mesmo manipulador de eventos, é importante não supor que os eventos sejam de uma fonte específica, mas para verificar o tópico de mensagem a fim de garantir que ela venha da conta de armazenamento que você está esperando.
* Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
* Ignore os campos que você não entende.  Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.
* Use a correspondência de prefixo e sufixo "subject" para limitar os eventos para um evento específico.

> [!NOTE]
> Os eventos estão sujeitos à Contrato de Nível de Serviço de grade de eventos [(SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Se você quiser obter notificações de eventos usando APIs, consulte exemplos de como consumir eventos, com o SDK do [.net](https://github.com/Azure-Samples/media-services-v3-dotnet) ou o [SDK do Java](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Próximas etapas

* [Monitorar eventos-Portal](monitor-events-portal-how-to.md)
* [Monitorar eventos - CLI](job-state-events-cli-how-to.md)
