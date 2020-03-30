---
title: APIs de serviço de medição de marketplace - FAQ | Mercado Azure
description: Emita o uso de uma oferta SaaS no Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275774"
---
# <a name="marketplace-metering-service-apis---faq"></a>APIs de serviço de medição do Marketplace – perguntas frequentes

Uma vez que um usuário do Azure assine um serviço SaaS que inclui faturamento medido, você acompanhará o consumo para cada dimensão de cobrança que está sendo usada pelo cliente. Se o consumo exceder as quantidades incluídas definidas para o termo selecionado pelo cliente, seu serviço emitirá eventos de uso para a Microsoft.

## <a name="emit-usage-events"></a>Emita eventos de uso

>[!Note]
>Esta seção é aplicável apenas para ofertas SaaS, onde pelo menos um dos planos tem dimensões de serviço de medição definidas no momento da publicação da oferta.

![Emita eventos de uso](media/isv-emits-usage-event.png)

Consulte a [API de uso em lote do SaaS](./marketplace-metering-service-apis.md#batch-usage-event) para obter informações sobre o contrato de API para emissão de eventos de uso.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Com que frequência se espera que ele emita uso?

O ideal é que você emita o uso a cada hora na última hora, apenas se houver uso na hora anterior.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Qual é o atraso máximo entre o tempo que um evento ocorre e o momento em que um evento de uso é emitido para a Microsoft?

Idealmente, o evento de uso é emitido a cada hora para eventos ocorridos na última hora. No entanto, são esperados atrasos. O atraso máximo permitido é de 24 horas, após os quais os eventos de uso não serão aceitos.

Por exemplo, se um evento de uso ocorre às 13:00 em um dia, você tem até às 13:00 no dia seguinte para emitir um evento de uso associado a este evento. Isso significa que no caso do sistema emissor de uso tem um tempo de inatividade, ele pode recuperar e, em seguida, enviar o evento de uso para o intervalo de horas em que o uso aconteceu, sem perda de fidelidade.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>O que acontece quando você envia mais de um evento de uso na mesma hora?

Apenas um evento de uso é aceito para o intervalo de horas. O intervalo de horas começa no minuto 0 e termina no minuto 59.  Se mais de um evento de uso for emitido para o mesmo intervalo de hora, quaisquer eventos de uso subseqüentes serão descartados como duplicatas.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>O que acontece quando você emite o uso de uma assinatura SaaS que já foi cancelada?

Qualquer evento de uso emitido para a plataforma de marketplace não será aceito após a exclusão de uma assinatura SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Você pode obter uma lista de todas as assinaturas SaaS, incluindo assinaturas ativas e não subscritas?

Sim, quando você `GET /saas/subscriptions` chama a API, ela inclui uma lista de todas as assinaturas saas. O campo de status na resposta para cada assinatura SaaS captura se a assinatura está ativa ou não. A chamada para listar Assinaturas retorna um máximo de 100 assinaturas no momento.

## <a name="next-steps"></a>Próximas etapas

- Consulte [APIs de serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para obter mais informações.
