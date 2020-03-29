---
title: Recuperação de desastres geonais na Grade de Eventos do Azure | Microsoft Docs
description: Descreve como o Azure Event Grid suporta geo recuperação de desastres (GeoDR) automaticamente.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307311"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Recuperação de desastres geográficos do lado do servidor no Azure Event Grid
Event Grid agora tem uma recuperação automática de desastres geográficos (GeoDR) de meta-dados não apenas para novos, mas todos os domínios, tópicos e assinaturas de eventos existentes. Se toda uma região do Azure cair, a Event Grid já terá todos os metadados de infra-estrutura relacionados a eventos sincronizados com uma região emparelhada. Seus novos eventos começarão a fluir novamente sem nenhuma intervenção sua. 

A recuperação de desastres é medida com duas métricas:

- [Objetivo do Ponto de Recuperação (RPO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)as atas ou horas de dados que podem ser perdidos.
- [Objetivo do Tempo de Recuperação (RTO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)as atas de horas que o serviço pode estar em baixa.

O failover automático do Event Grid tem diferentes RPOs e RTOs para seus metadados (assinaturas de eventos etc.) e dados (eventos). Se você precisar de especificações diferentes das seguintes, você ainda pode implementar sua própria [falha do lado do cliente sobre o uso do tópico apis de saúde](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>RPO (Objetivo de Ponto de Recuperação)
- **Metadados RPO**: zero minutos. Sempre que um recurso é criado no Event Grid, ele é replicado instantaneamente entre as regiões. Quando ocorre um failover, nenhum metadados é perdido.
- **Data RPO**: Se o seu sistema estiver saudável e alcançar o tráfego existente no momento do failover regional, o RPO para eventos é de cerca de 5 minutos.

## <a name="recovery-time-objective-rto"></a>RTO (Objetivo de Tempo de Recuperação)
- **Metadados RTO**: Embora geralmente aconteça muito mais rapidamente, dentro de 60 minutos, event grid começará a aceitar criar/atualizar/excluir chamadas para tópicos e assinaturas.
- **Data RTO**: Como metadados, geralmente acontece muito mais rapidamente, no entanto, dentro de 60 minutos, event grid começará a aceitar novos tráfegos após um failover regional.

> [!NOTE]
> O custo para metadados GeoDR na Event Grid é de: $0.


## <a name="next-steps"></a>Próximas etapas
Se você quiser implementar sua própria lógica de failover do lado do cliente, consulte [# Construa sua própria recuperação de desastres para tópicos personalizados no Event Grid](custom-disaster-recovery.md)
