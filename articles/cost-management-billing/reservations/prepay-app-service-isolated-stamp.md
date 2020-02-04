---
title: Salvar para o Serviço de Aplicativo do Azure com capacidade reservada
description: Saiba como você pode economizar custos com o Imposto de Selo do Serviço de Aplicativo Isolado do Azure com a capacidade reservada.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 8161b169e8801fae4a9aa81290cf29ec28abf858
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995878"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Economizar custos com o Imposto de Selo do Serviço de Aplicativo Isolado do Azure com a capacidade reservada

Você pode economizar dinheiro com o Imposto de Selo do Serviço de Aplicativo Isolado do Azure comprometendo-se com uma reserva de uso do selo por uma duração de três anos. Para comprar a capacidade reservada do Imposto de Selo Isolado, você precisa escolher a região do Azure em que o selo será implantado e o número de selos a serem comprados.

Quando você compra uma reserva, o uso do Imposto de Selo Isolado que corresponde aos atributos da reserva deixa de ser cobrado segundo as taxas de pagamento conforme o uso. A reserva é aplicada automaticamente ao número de selos isolados que correspondem à região e ao escopo da capacidade reservada. Você não precisa atribuir uma reserva a um selo isolado. A reserva não se aplica a funções de trabalho, de forma que todos os outros recursos associados ao selo são cobrados separadamente.

Quando a capacidade reservada expira, os Selos Isolados continuam sendo executados, mas são cobrados pela taxa de pagamento conforme o uso. As reservas não são renovadas automaticamente.

## <a name="determine-the-right-reservation-to-purchase"></a>Determinar a reserva correta a ser comprada

Ao comprar uma reserva, você está se comprometendo a usar as quantidades reservadas nos próximos três anos. Verifique seus dados de uso para determinar quantos Selos do Serviço de Aplicativo Isolado você está usando consistentemente e pode usar no futuro.

Além disso, certifique-se de entender como o Selo Isolado emite o medidor do Linux ou do Windows.

- Por padrão, um Selo Isolado vazio emite o medidor de selo do Windows. Por exemplo, sem trabalhos implantados. Ele continuará emitindo esse medidor se trabalhos do Windows forem implantados no selo.
- O medidor mudará para o medidor de selo do Linux se você implantar um trabalho do Linux.
- Em casos em que trabalhos do Linux e do Windows são implantados, o selo emite o medidor do Windows.

Portanto, o medidor do selo pode alternar entre o Windows e o Linux durante a vida útil do selo.

Compre reservas de selo do Windows se você tiver um ou mais trabalhos do Windows no selo. O único caso em que você deve comprar uma reserva de selo do Linux é quando planeja ter _apenas_ trabalhos do Linux no selo.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Comprar capacidade reservada do Selo Isolado

Você pode comprar capacidade reservada do Selo Isolado no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](monthly-payments-reservations.md). Para comprar capacidade reservada, é necessário ter a função de proprietário de pelo menos uma assinatura Enterprise ou uma assinatura individual com tarifas pagas conforme o uso.

- Para assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal do EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você precisará ser um administrador de EA.
- Para o programa Provedor de Solução de Nuvem (CSP), apenas os agentes admin ou agentes de vendas podem adquirir a capacidade reservada do SQL Data Warehouse.

**Para comprar:**

1. Vá para o [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selecione uma assinatura. Use a lista **Assinatura** para escolher a assinatura usada para pagar a capacidade reservada. A forma de pagamento da assinatura é cobrada nos custos da capacidade reservada. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P) ou uma assinatura de CSP.
    - Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média.
    - Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.
1. Selecione **Escopo** para escolher um escopo da assinatura.
    - **Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.
    - **Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.
    - **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do Contrato Enterprise, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.
1. Selecione uma **Região** para escolher uma região do Azure coberta pela capacidade reservada e adicione a reserva ao carrinho.
1. Selecione um tipo de Plano Isolado e clique em **Selecionar**.  
    ![Exemplo ](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Insira a quantidade de selos do Serviço de Aplicativo Isolado a serem reservados. Por exemplo, uma quantidade de três forneceria três selos reservados por região. Clique em **Avançar: Examinar + Comprar**.
1. Examine e clique em **Comprar agora**.

Após a compra, vá até [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) para exibir o status de da compra e monitorá-la a qualquer momento.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Aplicação do desconto mostrada nos dados de uso

Seus dados de uso têm um preço efetivo de zero para o uso, que obtém um desconto de reserva. Os dados de uso mostram o desconto de reserva para cada instância de selo em cada reserva.

Para saber mais sobre como o desconto de reserva é mostrado nos dados de uso, confira [Obter custos e uso de reserva do Contrato Enterprise](understand-reserved-instance-usage-ea.md) se você for um cliente com EA (Contrato Enterprise). Caso contrário, confira [Entenda o uso de reserva do Azure para uma assinatura individual com taxas pagas conforme o uso](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
  - [Entenda como um desconto de reserva de Selo do Serviço de Aplicativo Isolado do Azure é aplicado](reservation-discount-app-service-isolated-stamp.md)
  - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
