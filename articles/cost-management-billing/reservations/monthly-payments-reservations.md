---
title: Comprar reservas do Azure com pagamentos antecipados ou mensais
description: Saiba como você pode comprar reservas do Azure com pagamentos antecipados ou mensais.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: ede60adc13dadc38e18ee5ade468e01b16523f4f
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235738"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Comprar reservas com pagamentos mensais

Você pode pagar por elas fazendo pagamentos mensais. Diferente de uma compra antecipada em que você paga o valor total, a forme de pagamento mensal divide o custo total da reserva uniformemente em cada mês do termo. O custo total das reservas antecipadas e mensais é o mesmo e você não paga nenhuma taxa adicional quando opta por pagar mensalmente.

Se a reserva for comprada usando o MCA (Contrato de Cliente da Microsoft), o valor do pagamento mensal poderá variar de acordo com o câmbio da moeda local do mercado para o mês corrente.

Os pagamentos mensais não estão disponíveis para: Databricks, reservas do SUSE Linux, planos de Red Hat e Computação do Red Hat OpenShift no Azure.

Compre as reservas no [portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs).

![Exemplo mostrando a compra da reserva](./media/monthly-payments-reservations/purchase-reservation.png)

Ao fazer uma compra de reserva, você pode exibir o agendamento de pagamento. Clique em **Exibir a agenda de pagamento completo**.

![Exemplo mostrando a agenda de pagamento da reserva](./media/monthly-payments-reservations/prepurchase-schedule.png)

Para exibir a agenda de pagamentos após a compra, selecione uma reserva, clique na **ID do pedido de reserva** e clique na guia **Pagamentos**.

## <a name="view-payments-made"></a>Exibir pagamentos feitos

Você pode exibir os pagamentos que foram feitos usando APIs, dados de uso e análise de custo. Para reservas pagas mensalmente, o valor de frequência é mostrado como **recorrente** nos dados de uso e na API de Preços de Reserva. Para reservas pagas antecipadamente, o valor mostrado é **avulso**.

A análise de custo mostra as compras mensais na exibição padrão. Aplique o filtro de **compra** para o **Tipo de preço** e **recorrente** para a **Frequência** para ver todas as compras. Para exibir somente as reservas, aplique um filtro para **Reserva**.

![Exemplo mostrando os custos de compra da reserva na análise de custo](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Alternar para pagamentos mensais na renovação

Ao renovar uma reserva, você pode alterar a frequência de cobrança para mensal.

## <a name="exchange-and-refunds"></a>Trocas e reembolsos

Assim como outras reservas, você pode reembolsar ou trocar reservas adquiridas com cobrança mensal. 

Quando você troca uma reserva paga mensalmente, o custo total do tempo de vida da nova compra deve ser maior do que os pagamentos restantes cancelados referentes à reserva devolvida. Não há outros limites ou taxas para trocas. Você pode trocar uma reserva paga antecipadamente para comprar uma nova reserva cobrada mensalmente. No entanto, o valor do tempo de vida da nova reserva deve ser maior que o valor rateado da reserva que está sendo devolvida.

Se você cancelar uma reserva paga mensalmente, os pagamentos futuros cancelados se acumularão até o limite de reembolso de US$ 50.000.

Para saber mais sobre trocas e reembolsos, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre reservas, confira [O que são Reservas do Azure?](save-compute-costs-reservations.md)
- Para saber mais sobre as tarefas que você deve realizar antes de comprar uma reserva, confira [Determinar o tamanho correto da VM antes de comprar](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
