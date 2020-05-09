---
title: Como as recomendações de reserva do Azure são criadas
description: Saiba como as recomendações de reserva do Azure são criadas para máquinas virtuais.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 90967e740b87c2f93bd46bfb78684af96f36193a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508472"
---
# <a name="how-reservation-recommendations-are-created"></a>Como as recomendações de reserva são criadas

As recomendações de compra de RI (instância reservada) do Azure são fornecidas por meio da [API de Recomendação de Reserva](/rest/api/consumption/reservationrecommendations) e do [Assistente do Azure](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) do Azure Consumption e também por meio da experiência de compra de reserva no portal do Azure.

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
