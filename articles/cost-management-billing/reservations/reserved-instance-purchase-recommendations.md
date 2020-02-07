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
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851344"
---
# <a name="how-reservation-recommendations-are-created"></a>Como as recomendações de reserva são criadas

As recomendações de compra de RI (instância reservada) do Azure são geradas pela [API de Recomendação de Reserva](/rest/api/consumption/reservationrecommendations) de Consumo do Azure. As recomendações da API também são usadas pelo [Assistente do Azure](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs). O Assistente mostra recomendações no portal do Azure.

Se você tiver VMs em execução no Azure, poderá aproveitar os preços com desconto para RIs e pagar antecipadamente por suas VMs. A API de Recomendação de Consumo da Microsoft avalia seu uso para 7, 30 e 60 dias e recomenda as configurações ideais para RIs. Ela calcula o custo que você pagaria se não tivesse RIs em relação ao custo que pagará com RIs para otimizar a economia.

O Assistente do Azure mostra recomendações com base em um período de 30 dias.

Para simplificar, o exemplo a seguir mostra os cálculos feitos para uma recomendação de sete dias. O mesmo método é aplicado ao calcular recomendações de 30 ou 60 dias.

## <a name="calculation-method-example"></a>Exemplo do método de cálculo

Suponha que seu uso de VM do Windows por hora para uma determinada SKU e região varia mais de sete dias (168 horas). O uso mínimo é de 65 unidades e o uso máximo é de 127 unidades durante os sete dias. Neste exemplo, a hora 79 usou 80 VMs e você comprou 75 RIs.

Se você comprar 75 instâncias reservadas, pagará os seguintes custos para a hora 79:

- 75 instâncias reservadas. O custo é pago antecipadamente quando você compra RIs.
- As instâncias reservadas abrangem o custo de hardware das VMs em execução, portanto, você pagará por 75 horas no preço somente de software.
- O uso da hora 79 é 80, portanto, você pagará por cinco horas do Windows mais o preço do medidor de combinação de hardware. O preço de combinação se baseia na sua taxa de EA (Contrato Enterprise) ou paga conforme o uso.

Se você comprar 75 RIs, poderá calcular o custo total adicionando os custos por hora anteriores. Você também pode calcular seu custo atual usando sua taxa. A diferença entre as duas quantidades é a sua economia para sete dias neste exemplo.

A API faz cálculos para cada ponto de uso específico. Em seguida, ela retorna a quantidade recomendada na qual as economias são maximizadas. No exemplo a seguir, o grafo mostra o pico de economia em 68. As economias são reduzidas posteriormente, portanto, a API recomenda 68.

![Diagrama mostrando os picos de economia](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>Outro comportamento de API esperado

- A API mostra possíveis economias com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para Windows quando o benefício é usado. Se o benefício não for usado, as recomendações de API serão baseadas no custo principal do Windows. Se estiver disponível para você, considere o uso do Benefício Híbrido do Azure para aumentar a economia.
- Ao usar um período de pesquisa de sete dias, você talvez não obtenha recomendações quando as VMs forem desligadas por mais de um dia.

## <a name="next-steps"></a>Próximas etapas
- Saiba [como é aplicado o desconto de reserva do Azure a máquinas virtuais](../manage/understand-vm-reservation-charges.md).
