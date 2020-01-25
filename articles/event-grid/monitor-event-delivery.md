---
title: Monitorar a entrega de mensagens da Grade de Eventos do Azure
description: Este artigo descreve como usar o portal do Azure para ver o status da entrega de mensagens da grade de eventos do Azure.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722128"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorar a entrega de mensagens da Grade de Eventos 

Este artigo descreve como usar o portal para ver o status de entregas de evento.

A entrega proporcionada pela Grade de Eventos tem um tempo de duração. Cada mensagem é entregue pelo menos uma vez para cada assinatura. Os eventos são imediatamente enviados ao webhook registrado de cada assinatura. Se um webhook não confirmar o recebimento de um evento em até 60 segundos a contar da primeira tentativa de entrega, a Grade de Eventos repetirá a entrega do evento.

Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Métricas de entrega

O portal exibe a métrica do status de entrega das mensagens de evento.

Para tópicos, as métricas são:

* **Publicação bem-sucedida**: evento enviado com êxito para o tópico e processado com uma resposta 2xx.
* **Falha na Publicação**: evento enviado para o tópico, mas rejeitado com um código de erro.
* **Sem correspondência**: evento publicado com êxito para o tópico, mas não corresponde a uma assinatura de evento. O evento foi cancelado.

Para assinaturas, as métricas são:

* **Entrega bem-sucedida**: evento entregue com êxito ao ponto de extremidade da assinatura e recebeu uma resposta de 2xx.
* **Falha de entrega**: evento enviado ao ponto de extremidade da assinatura, mas recebeu uma resposta 4xx ou 5xx.
* **Eventos Expirados**: evento não foi entregue e todas as tentativas de repetição foram enviadas. O evento foi cancelado.
* **Eventos correspondidos**: evento no tópico foi correspondido pela assinatura do evento.

## <a name="event-subscription-status"></a>Status da assinatura do evento

Para ver as métricas da assinatura de um evento, você poderá pesquisar por tipo de assinatura ou por assinaturas de um recurso específico.

Para pesquisar por tipo de assinatura de evento, selecione **Todos os serviços**.

![Selecionar todos os serviços](./media/monitor-event-delivery/all-services.png)

Pesquise por **grade de eventos** e selecione **Assinaturas de Grade de Eventos** entre as opções disponíveis.

![Pesquisar assinaturas de evento](./media/monitor-event-delivery/search-and-select.png)

Filtre por tipo de evento, assinatura e localização. Selecione **Métricas** da assinatura para exibir.

![Filtrar assinaturas de evento](./media/monitor-event-delivery/filter-events.png)

Exiba as métricas do tópico e assinatura do evento.

![Exibir métricas de evento](./media/monitor-event-delivery/subscription-metrics.png)

Para localizar as métricas de um recurso específico, selecione o recurso. Em seguida, selecione **Eventos**.

![Selecionar eventos para um recurso](./media/monitor-event-delivery/select-events.png)

Você verá as métricas das assinaturas desse recurso.

## <a name="custom-event-status"></a>Status de evento personalizado

Se tiver publicado um tópico personalizado, você poderá exibir as métricas dele. Selecione o grupo de recursos do tópico e selecione o tópico.

![Selecionar tópico personalizado](./media/monitor-event-delivery/select-custom-topic.png)

Exiba as métricas do tópico de evento personalizado.

![Exibir métricas de evento](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Definir alertas

Você pode definir alertas sobre as métricas de nível de domínio e de tópico para tópicos personalizados e domínios de evento. Na folha visão geral do, selecione **alertas** no menu de recursos da esquerda para exibir, gerenciar e criar regras de alerta. [Saiba mais sobre alertas de Azure Monitor](../azure-monitor/platform/alerts-overview.md)

![Exibir métricas de evento](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Próximos passos

* Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
