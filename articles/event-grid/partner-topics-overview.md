---
title: Tópicos de parceiro da Grade de Eventos do Azure
description: Envie eventos de parceiros de PaaS e SaaS da Grade de Eventos de terceiros diretamente para os serviços do Azure com a Grade de Eventos do Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 97829f262a00e2353624b2a3fa9df210518ad3a0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115988"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Tópicos de parceiro na grade de eventos do Azure (visualização)
Usando tópicos de parceiros, você pode conectar fontes de eventos de terceiros diretamente à grade de eventos do Azure. Essa integração permite que você assine eventos de parceiros da mesma maneira que assina eventos dos serviços do Azure. 

## <a name="available-partners"></a>Parceiros disponíveis
O primeiro parceiro disponível por meio de tópicos de parceiros de grade de eventos é Auth0. Você pode usar o [tópico de parceiro do Auth0](auth0-overview.md) para conectar suas contas do Auth0 e do Azure. A integração permite reagir, registrar e monitorar eventos Auth0 em tempo real.

[Para experimentar](auth0-how-to.md), entre em sua conta do Auth0 e crie uma integração da grade de eventos. Depois de selecionar **criar** no Auth0, você verá um tópico de Auth0 pendente em sua conta do Azure. Selecione **Ativar**e você pode criar assinaturas de grade de eventos para rotear, filtrar e entregar seus eventos da mesma forma que qualquer outra fonte de evento.

## <a name="pricing"></a>Preços
Os tópicos de parceiros são cobrados com a mesma taxa de operação que os tópicos do sistema.

## <a name="limits"></a>limites
Os tópicos de parceiros estão em visualização pública. Durante a visualização pública, os tópicos de parceiros estão sujeitos aos [mesmos limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) que os tópicos do sistema e os tópicos personalizados.

## <a name="how-do-i-become-an-event-grid-partner"></a>Como fazer para eu me tornar um parceiro da Grade de Eventos?
A infraestrutura criada para dar suporte a esse lançamento torna fácil e rápido a integração de funcionalidades de eventos de novos parceiros com a Grade de Eventos. Para obter mais informações, consulte a [documentação de integração do parceiro](partner-onboarding-overview.md).

## <a name="next-steps"></a>Próximas etapas

- [Tópico de parceiro do Auth0](auth0-overview.md)
- [Como usar o tópico de parceiro do Auth0](auth0-how-to.md)
- [Torne-se um Parceiro da Grade de Eventos](partner-onboarding-overview.md)