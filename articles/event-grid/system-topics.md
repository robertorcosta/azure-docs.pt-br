---
title: Tópicos do sistema na grade de eventos do Azure
description: Descreve os tópicos do sistema na grade de eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393161"
---
# <a name="system-topics-in-azure-event-grid"></a>Tópicos do sistema na grade de eventos do Azure
O serviço de grade de eventos do Azure cria tópicos do sistema ao criar uma primeira assinatura de evento para uma origem de evento do Azure. Atualmente, a grade de eventos não cria tópicos do sistema para fontes de tópico que foram criadas antes de mar, 15, 2020. Para todas as fontes de tópico criadas nesta data ou depois dela, a grade de eventos cria automaticamente tópicos do sistema. Este artigo descreve os **Tópicos do sistema** na grade de eventos do Azure.

> [!NOTE]
> Este recurso não está habilitado no momento para a nuvem do Azure governamental. 

## <a name="overview"></a>Visão geral
Quando você cria a primeira assinatura de evento para uma fonte de eventos do Azure, como a conta de armazenamento do Azure, o processo de provisionamento para a assinatura cria um recurso adicional do tipo **Microsoft. EventGrid/systemTopics**. Quando a última assinatura de evento para a origem do evento do Azure é excluída, o tópico do sistema é excluído automaticamente.

O tópico do sistema não é aplicável a cenários de tópico personalizados, ou seja, tópicos de grade de eventos e domínios de grade de eventos. 

## <a name="location"></a>Local
Para as fontes de eventos do Azure que estão em uma região/local específica, o tópico do sistema é criado no mesmo local que a origem do evento do Azure. Por exemplo, se você criar uma assinatura de evento para um armazenamento de BLOBs do Azure no leste dos EUA, o tópico do sistema será criado no leste dos EUA. Para fontes de eventos globais do Azure, como assinaturas do Azure, grupos de recursos ou mapas do Azure, a grade de eventos cria o tópico do sistema no local **global** . 

## <a name="resource-group"></a>Resource group 
Em geral, o tópico do sistema é criado no mesmo grupo de recursos em que a origem do evento do Azure está. Para assinaturas de evento criadas no escopo de assinatura do Azure, o tópico do sistema é criado sob o grupo de recursos **Default-EventGrid**. Se o grupo de recursos não existir, a grade de eventos do Azure o criará antes de criar o tópico do sistema. 

Ao tentar excluir o grupo de recursos com a conta de armazenamento, você verá o tópico sistema na lista de recursos afetados.  

![Excluir grupo de recursos](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 

- [Tópicos personalizados](custom-topics.md)
- [Domínios](event-domains.md)