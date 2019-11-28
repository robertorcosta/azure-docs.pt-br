---
title: Economizar custos com o preço do SQL Data Warehouse com a capacidade reservada do Azure
description: Saiba como economizar custos com o preço do SQL Data Warehouse com a capacidade reservada para economizar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 75e008a6a19baacec40a809530e3a1f624a33f2b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223615"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Economizar custos com o preço do SQL Data Warehouse com a capacidade reservada

Você pode economizar com o SQL Data Warehouse do Azure comprometendo-se com uma reserva para o uso de cDWU previamente durante um ou três anos. Para comprar uma capacidade reservada do SQL Data Warehouse, você precisa escolher a região do Azure e o termo. Em seguida, adicione a SKU (unidade de manutenção de estoque) do SQL Data Warehouse ao seu carrinho e escolha a quantidade de unidades de cDWU que deseja comprar.

Quando você compra uma reserva, o uso do SQL Data Warehouse, que corresponde aos atributos da reserva, não é mais cobrado pelas taxas de pagamento conforme o uso.

A reserva não cobre encargos de armazenamento ou rede associados ao uso do SQL Data Warehouse.

Quando a capacidade reservada expira, as instâncias do SQL Data Warehouse continuam a ser executadas, mas são cobradas pela taxa de pagamento conforme o uso. As reservas não são renovadas automaticamente.

Para obter informações sobre preços, confira a [oferta de capacidade reservada do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Você pode comprar capacidade reservada do SQL Data Warehouse do Azure no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Pague pela reserva [antecipadamente ou com pagamentos mensais](billing-monthly-payments-reservations.md). Para comprar uma capacidade reservada:

- Você precisa ter a função de proprietário de pelo menos uma assinatura Enterprise ou paga conforme o uso.
- Para assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal do EA](https://ea.azure.com/). Se essa configuração estiver desabilitada, você precisará ser um administrador de EA.
- Para o programa Provedor de Solução de Nuvem (CSP), apenas os agentes admin ou agentes de vendas podem adquirir a capacidade reservada do SQL Data Warehouse.

Para saber mais sobre como clientes empresariais e clientes do método de Pagamento Conforme o Uso são cobrados para compras de reserva, confira [Entenda o uso de reserva do Azure para seu Registro Enterprise](billing-understand-reserved-instance-usage-ea.md) e [Entenda o uso de reserva do Azure para sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Escolher o tamanho correto antes da compra

O tamanho da reserva do SQL Data Warehouse deve ser baseado no total de cDWU (unidades de data warehouse de computação) que você consome. As compras são feitas em incrementos de 100 cDWU.

Por exemplo, suponha que o consumo total do SQL Data Warehouse seja de DW3000c. Você deseja comprar a capacidade reservada referente a todo esse consumo. Sendo assim, você precisa comprar 30 unidades de capacidade reservada de cDWU.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Comprar capacidade reservada do SQL Data Warehouse

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione uma assinatura. Use a lista Assinatura para escolher a assinatura usada para pagar a capacidade reservada. A forma de pagamento da assinatura é cobrada nos custos da capacidade reservada. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P).
   - Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média.
   - Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.
4. Selecione um escopo. Use a lista Escopo para escolher um escopo da assinatura.
   - **Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.
   - **Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.
   - **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do Contrato Enterprise, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.
   - Para clientes empresariais, o contexto de cobrança é o registro do EA.
   - Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.
5. Selecione uma região a fim de escolher a região do Azure abrangida pela capacidade reservada.
6. Escolha uma quantidade. Insira a quantidade de unidades de 100 cDWUs (unidades de data warehouse) que você deseja comprar.    
   Por exemplo, uma quantidade de 30 forneceria 3.000 cDWU de capacidade reservada por hora.
7. Examine o custo da reserva de capacidade reservada do SQL Data Warehouse na seção **Custos**.
8. Selecione **Comprar**.
9. Selecione **Exibir essa Reserva** para ver o status de sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

Um desconto de reserva é aplicado automaticamente ao número de instâncias do SQL Data Warehouse que correspondem à região e ao escopo da capacidade reservada do SQL Data Warehouse. Você pode atualizar o escopo da capacidade reservada do SQL Data Warehouse por meio do [Portal do Azure](https://portal.azure.com/), PowerShell, CLI ou por meio da API.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como os descontos de reserva se aplicam ao SQL Data Warehouse do Azure, confira [Como os descontos de reserva se aplicam ao Azure SQL Data Warehouse do Azure](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
  - [Compreender o desconto de Reservas do Azure](billing-understand-reservation-charges.md)
  - [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
  - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
