---
title: Comprar reservas do Azure com pagamentos antecipados ou mensais
description: Saiba como você pode comprar reservas do Azure com pagamentos antecipados ou mensais.
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: banders
ms.openlocfilehash: 89ae15bb82db1e9e06ac2cb52146137161dac47d
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031332"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Comprar reservas com pagamentos mensais

Até agora, as reservas do Azure exigiam pagamento antecipado. Agora, você pode pagar por elas fazendo pagamentos mensais. Diferente de uma compra antecipada em que você paga o valor total, a forme de pagamento mensal divide o custo total da reserva uniformemente em cada mês do termo. O custo total das reservas antecipadas e mensais é o mesmo e você não paga nenhuma taxa adicional quando opta por pagar mensalmente.

O valor do pagamento mensal poderá variar de acordo com o câmbio da moeda local do mercado para o mês corrente.

Os pagamentos mensais estão disponíveis para:

- Máquinas virtuais
- Armazenamento do Azure
- BANCOS DE DADOS SQL
- SQL Data Warehouse
- Cosmos DB
- Imposto de selo do Serviço de Aplicativo
- Disco Gerenciado
- Azure Data Explorer
- Banco de Dados do Azure para MariaDB, MySQL e PostgreSQL
- Solução VMware no Azure pela CloudSimple


Compre as reservas no [portal do Azure](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Exemplo mostrando a compra da reserva](./media/monthly-payments-reservations/purchase-reservation.png)

Ao fazer uma compra de reserva, você pode exibir o agendamento de pagamento. Clique em **Exibir a agenda de pagamento completo**.

![Exemplo mostrando a agenda de pagamento da reserva](./media/monthly-payments-reservations/prepurchase-schedule.png)

Para exibir a agenda de pagamentos após a compra, selecione uma reserva, clique na **ID do pedido de reserva** e clique na guia **Pagamentos**.

## <a name="view-payments-made"></a>Exibir pagamentos feitos

Você pode exibir os pagamentos que foram feitos usando APIs, dados de uso e análise de custo. Para reservas pagas mensalmente, o valor de frequência é mostrado como **recorrente** nos dados de uso e na API de Preços de Reserva. Para reservas pagas antecipadamente, o valor mostrado é **avulso**.

A análise de custo mostra as compras mensais na exibição padrão. Aplique o filtro de **compra** para o **Tipo de encargo** e **recorrente** para a **Frequência** para ver todas as compras. Para exibir somente as reservas, aplique um filtro para **Reserva**.

![Exemplo mostrando os custos de compra da reserva na análise de custo](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Alternar para pagamentos mensais na renovação

Ao renovar uma reserva, você pode alterar a frequência de cobrança para mensal.

## <a name="exchange-and-refunds"></a>Trocas e reembolsos

Assim como outras reservas, você pode reembolsar ou trocar reservas adquiridas com cobrança mensal. No momento, você pode enviar uma solicitação de suporte para iniciar uma troca ou reembolso para uma reserva adquirida com cobrança mensal.

Quando você troca uma reserva paga mensalmente, o custo total do tempo de vida da nova compra deve ser maior do que os pagamentos restantes cancelados referentes à reserva devolvida. Não há outros limites ou taxas para trocas. Você pode trocar uma reserva paga antecipadamente para comprar uma nova reserva cobrada mensalmente. No entanto, o valor do tempo de vida da nova reserva deve ser maior que o valor rateado da reserva que está sendo devolvida.

Ao cancelar uma reserva paga mensalmente, a Microsoft poderá aplicar uma taxa de cancelamento aos futuros pagamentos confirmados que foram cancelados. Os pagamentos confirmados restantes se acumulam até o limite de reembolso de US$ 50.000.

Para saber mais sobre trocas e reembolsos, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre reservas, confira [O que são Reservas do Azure?](save-compute-costs-reservations.md)
- Para saber mais sobre as tarefas que você deve realizar antes de comprar uma reserva, confira [Determinar o tamanho correto da VM antes de comprar](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
