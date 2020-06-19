---
title: Tópicos de parceiro da Grade de Eventos do Azure
description: Envie eventos de parceiros de PaaS e SaaS da Grade de Eventos de terceiros diretamente para os serviços do Azure com a Grade de Eventos do Azure.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690032"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Tópicos de Parceiros na Grade de Eventos do Azure (versão prévia)
Os Tópicos de Parceiros permitem que você conecte fontes origens do evento de terceiros diretamente à Grade de Eventos. Essa integração permite que você assine eventos de parceiros da mesma maneira que assina eventos dos serviços do Azure. 

## <a name="available-partners"></a>Parceiros disponíveis
O primeiro parceiro disponível por meio de Tópicos de Parceiros da Grade de Eventos é Auth0. O [tópico de parceiros do Auth0](auth0-overview.md) permitem que você conecte suas contas do Auth0 e do Azure. A integração para reagir a eventos do Auth0, bem como registrá-los e monitorá-los em tempo real.

[Experimente](auth0-how-to.md) hoje mesmo fazendo logon em sua conta do Auth0 e criando uma integração da Grade de Eventos. Depois de clicar em criar no Auth0, você verá um tópico do Auth0 pendente em sua conta do Azure. Clique em ativar e você poderá criar Assinaturas de Evento, rotear, filtrar e entregar seus eventos assim como você faz com qualquer outra origem do evento.

## <a name="pricing"></a>Preços
Os tópicos de parceiros são cobrados com a mesma taxa de operação que os tópicos do sistema.

## <a name="limits"></a>limites
Os Tópicos de Parceiros estão em versão prévia pública. Durante a versão prévia pública, os Tópicos de Parceiros estão sujeitos aos [mesmos limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) que os tópicos do sistema e os tópicos personalizados.

## <a name="how-do-i-become-an-event-grid-partner"></a>Como fazer para eu me tornar um parceiro da Grade de Eventos?
A infraestrutura criada para dar suporte a esse lançamento torna fácil e rápido a integração de funcionalidades de eventos de novos parceiros com a Grade de Eventos. Leia a [documentação de integração do parceiro](partner-onboarding-overview.md) para saber mais.

## <a name="next-steps"></a>Próximas etapas

- [Tópico de parceiro do Auth0](auth0-overview.md)
- [Como usar o tópico de parceiro do Auth0](auth0-how-to.md)
- [Torne-se um Parceiro da Grade de Eventos](partner-onboarding-overview.md)