---
title: Tópicos do sistema na Grade de Eventos do Azure
description: Descreve os tópicos do sistema no Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 35025bf1592f1293b9326d643f76322b4af590c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501198"
---
# <a name="system-topics-in-azure-event-grid"></a>Tópicos do sistema na Grade de Eventos do Azure
O serviço Azure Event Grid cria tópicos do sistema quando você cria uma assinatura de primeiro evento para uma fonte de evento do Azure. Atualmente, a Event Grid não cria tópicos do sistema para fontes de tópicos que foram criados antes de 15 de março de 2020. Para todas as fontes de tópico susceadas nesta data ou após essa data, event grid cria automaticamente tópicos do sistema. Este artigo descreve **tópicos do sistema** no Azure Event Grid.

> [!NOTE]
> Este recurso atualmente não está habilitado para a nuvem do Governo Do Azure. 

## <a name="overview"></a>Visão geral
Quando você cria a assinatura do primeiro evento para uma fonte de evento do Azure, como a conta Azure Storage, o processo de provisionamento da assinatura cria um recurso adicional do tipo **Microsoft.EventGrid/systemTopics**. Quando a última assinatura de evento para a fonte de evento do Azure é excluída, o tópico do sistema é automaticamente excluído.

O tópico do sistema não se aplica a cenários de tópicos personalizados, ou seja, tópicos da Grade de Eventos e domínios da Grade de Eventos. 

## <a name="location"></a>Location
Para fontes de eventos do Azure que estão em uma região/local específica, o tópico do sistema é criado no mesmo local que a fonte do evento Azure. Por exemplo, se você criar uma assinatura de evento para um armazenamento de blob do Azure no leste dos EUA, o tópico do sistema será criado no Leste dos EUA. Para fontes globais de eventos do Azure, como assinaturas do Azure, grupos de recursos ou Mapas Do Azure, o Event Grid cria o tópico do sistema em localização **global.** 

## <a name="resource-group"></a>Resource group 
Em geral, o tópico do sistema é criado no mesmo grupo de recursos em que a fonte de evento do Azure está. Para assinaturas de eventos criadas no escopo de assinatura do Azure, o tópico do sistema é criado o grupo de recursos **Default-EventGrid**. Se o grupo de recursos não existir, o Azure Event Grid o criará antes de criar o tópico do sistema. 

Quando você tenta excluir o grupo de recursos com a conta de armazenamento, você verá o tópico do sistema na lista de recursos afetados.  

![Excluir grupo de recursos](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 

- [Tópicos personalizados](event-sources.md#custom-topics)
- [Domínios](event-domains.md)