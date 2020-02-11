---
title: Como as recomendações de reserva do Azure são criadas
description: Saiba como as recomendações de reserva do Azure são criadas para máquinas virtuais.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: 2f7b09c14553fdb5d642080d286ce123176b997f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991041"
---
# <a name="how-reservation-recommendations-are-created"></a>Como as recomendações de reserva são criadas
As recomendações de compra de RI (instância reservada) do Azure são fornecidas por meio da [API de Recomendação de Reserva](/rest/api/consumption/reservationrecommendations) e do [Assistente do Azure](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) do Azure Consumption e também por meio da experiência de compra de reserva no portal do Azure.

As seguintes etapas definem como as recomendações são calculadas: 
1. O mecanismo de recomendação avalia o uso por hora dos seus recursos no escopo fornecido durante os últimos 7, 30 e 60 dias.
2. Com base nos dados de uso, o mecanismo simula seus custos com e sem reservas.
3. Os custos são simulados para diferentes quantidades e a quantidade que maximiza a economia é recomendada.
4. Se os recursos forem desligados regularmente, a simulação não encontrará nenhuma economia e nenhuma recomendação de compra será fornecida.

## <a name="recommendations-in-azure-advisor"></a>Recomendações no Assistente do Azure
As recomendações de compra de reserva para máquinas virtuais estão disponíveis no Assistente do Azure. Tenha em mente os seguintes pontos: 
- O Assistente tem apenas recomendações de escopo de assinatura única.
- Recomendações calculadas com um período de retrospectiva de 30 dias estão disponíveis no Assistente.
- Se você comprar uma reserva com escopo compartilhado, as recomendações de compra de reserva do Assistente poderão levar 30 dias para desaparecer.

## <a name="other-expected-api-behavior"></a>Outro comportamento de API esperado
- Ao usar um período de retrospectiva de sete dias, talvez você não receba recomendações quando as VMs ficarem desligadas por mais de um dia.

## <a name="next-steps"></a>Próximas etapas
- Saiba [como é aplicado o desconto de reserva do Azure a máquinas virtuais](../manage/understand-vm-reservation-charges.md).
