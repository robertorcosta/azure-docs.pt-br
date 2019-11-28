---
title: Exibir reservas para recursos do Azure | Microsoft Docs
description: Saiba como exibir as Reservas do Azure no portal do Azure.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 21199a3f444fb6b6262e5f1b4884356dac42c3bb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225601"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Exibir as reservas do Azure no portal do Azure

Dependendo do tipo de assinatura e das permissões, há duas maneiras de exibir as reservas para o Azure.

## <a name="view-purchased-reservations"></a>Exibir as reservas adquiridas

Por padrão, quando você compra uma reserva, você e o administrador da conta podem exibir a reserva. Você e o administrador da conta obtêm automaticamente a função Proprietário no pedido de reserva e na reserva. Para permitir que outras pessoas exibam a reserva, você precisa adicioná-las como **Proprietário** ou **Leitor** no pedido de reserva ou na reserva.

Para obter mais informações, consulte [Adicionar ou alterar os usuários que podem gerenciar uma reserva](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Para exibir uma reserva como Proprietário ou Leitor,

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Pesquise **Reservas**.
    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-view-reservation/portal-reservation-search.png)  
3. A lista mostra todas as reservas nas quais você tem a função Proprietário ou Leitor. Cada reserva mostra o último percentual de utilização conhecido.  
    ![Exemplo mostrando uma lista de reservas](./media/billing-view-reservation/view-reservations.png)
4. Selecione uma reserva e veja a tendência de utilização dos últimos cinco dias.  
    ![Exemplo mostrando a tendência de utilização de reserva](./media/billing-view-reservation/reservation-utilization.png)
5. Obtenha também a [utilização de reserva](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) usando a API de Uso da Instância Reservada e com o [pacote de conteúdo do Power BI Microsoft Azure Consumption Insights](/power-bi/service-connect-to-azure-consumption-insights).

Se você precisar alterar o escopo de uma reserva, dividir uma reserva ou alterar quem pode gerenciar uma reserva, consulte [Gerenciar as Reservas do Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Exibir transações de reserva para inscrições do Enterprise

 Se você tiver um registro de Enterprise liderado pelo parceiro, exiba reservas acessando **Relatórios** no portal do EA. Para outros registros do Enterprise, você pode exibir as reservas no portal do EA e no portal do Azure. Você deve ser um administrador EA para exibir transações de reserva.

Exibir transações de reserva no portal do Azure,

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Selecione **Transações de reserva**.
1. Para filtrar os resultados, selecione **Timespan**, **Tipo**, ou **Descrição**.
1. Escolha **Aplicar**.

    ![Captura de tela que mostra os resultados de transações de reserva](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Para obter os dados usando uma API, consulte [Obter Cobranças de Transação de Instância Reservada para clientes corporativos](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

- [O que são reservas para o Azure?](billing-save-compute-costs-reservations.md)
- [Gerenciar reservas do Azure](billing-manage-reserved-vm-instance.md)

Comprar um plano de serviço:

- [Pagar antecipadamente por capacidade reservada do Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Banco de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Comprar um plano de software:

- [Pagar antecipadamente por planos de software Red Hat das reservas do Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Pré-pagamento para planos de software SUSE das reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Entender o uso:

- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Entenda o uso de reservas para o seu registro Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Entender o uso de reserva para assinaturas de CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
