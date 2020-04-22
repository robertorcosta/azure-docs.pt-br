---
title: Pré-pago para planos de software - Reservas Azure
description: Saiba como você pode pagar antecipadamente por planos de software para economizar dinheiro em relação aos seus custos de pagamento.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: a527b75d376f05ab6190187b7a03d6da775055ab
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759292"
---
# <a name="prepay-for-azure-software-plans"></a>Pagar antecipadamente planos de software do Azure

Quando você paga antecipadamente pelo uso do software SUSE e RedHat no Azure, você pode economizar dinheiro com os custos de pagamento. Os descontos só se aplicam aos medidores SUSE e RedHat e não no uso da máquina virtual. Você pode comprar reservas para máquinas virtuais separadamente para economias adicionais.

Você pode comprar planos de software SUSE e RedHat no portal Azure. Comprar um plano:

- Você deve ter a função de proprietário para pelo menos uma assinatura Enterprise ou individual com preços de pagamento à medida que você vai.
- Para assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal do EA](https://ea.azure.com/). Se a configuração estiver desativada, você deve ser um administrador da EA para a assinatura.
- Para o programa Cloud Solution Provider (CSP), os agentes de admin ou agentes de vendas podem comprar os planos de software.

## <a name="buy-a-software-plan"></a>Compre um plano de software

1. Faça login no portal Azure e vá para [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Clique **em Adicionar** e, em seguida, selecione o plano de software que deseja comprar.
Preencha os campos obrigatórios. Qualquer VM SUSE Linux ou RedHat VM que corresponda aos atributos do que você compra recebe o desconto. O número real de implantações que obtêm o desconto depende do escopo e da quantidade selecionada.
3. Selecione uma assinatura. É usado para pagar pelo plano.
O método de pagamento da assinatura é cobrado dos custos iniciais da reserva. O tipo de assinatura deve ser um Contrato Empresarial (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou acordo individual com preços de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P).
    - Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média.
    - Para uma assinatura individual com preços de pagamento, os encargos são cobrados no cartão de crédito da assinatura ou no método de pagamento da fatura.
4. Selecione um escopo. O escopo pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado).
    - Assinatura única - O desconto do plano é aplicado ao uso correspondente na assinatura.
    - Compartilhado - O desconto do plano é aplicado às instâncias correspondentes em qualquer assinatura em seu contexto de faturamento. Para clientes corporativos, o contexto de faturamento é a inscrição e inclui todas as assinaturas na inscrição. Para um plano individual com clientes de preços de pagamento, o contexto de faturamento é todo plano individual com assinaturas de preços de pagamento criadas pelo administrador da conta.
    - Grupo de recursos únicos - Aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.
5. Selecione um produto para escolher o tamanho da VM e o tipo de imagem. O desconto se aplica apenas ao tamanho da VM selecionada.
6. Selecione um mandato de um ano ou três anos.
7. Escolha uma quantidade, que é o número de instâncias de VM pré-pagas que podem obter o desconto de faturamento.
8. Adicione o produto ao carrinho, revise e compre.

O desconto de reserva é aplicado automaticamente ao medidor de software que você pré-paga. As taxas de computação vm não estão cobertas pelo plano. Você pode comprar as reservas de VM separadamente.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Desconto se aplica a diferentes tamanhos de VM suse

Como instâncias de VM reservadas, os planos Do SUSE Linux oferecem flexibilidade de tamanho de instância. Seu desconto se aplica mesmo quando você implanta uma VM de tamanho diferente do plano SUSE que você comprou. Para obter mais informações, consulte [Entenda como o desconto do plano de software é aplicado.](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)

## <a name="redhat-plan-discount"></a>Desconto do plano RedHat

Os planos estão disponíveis apenas para máquinas virtuais Red Hat Enterprise Linux. O desconto não se aplica aos VMs RedHat Enterprise Linux SAP HANA ou RedHat Enterprise Linux SAP Business Apps.

Os descontos do plano RedHat se aplicam apenas ao tamanho da VM que você selecionar no momento da compra. Os planos RHEL não podem ser reembolsados ou trocados após a compra.


## <a name="cancellation-and-exchanges-not-allowed"></a>Cancelamento e trocas não permitidas

Você não pode cancelar ou trocar um plano SUSE ou RedHat que você comprou. Verifique seu uso para garantir que você compre o plano certo. Para ajudar a identificar o que comprar, consulte [Entenda como o desconto do plano de software é aplicado.](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

Para saber como gerenciar uma reserva, consulte [Gerenciar reservas do Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Para saber mais, leia os seguintes artigos:

- [O que são Reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gerenciar reservas no Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva do SUSE é aplicado](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
