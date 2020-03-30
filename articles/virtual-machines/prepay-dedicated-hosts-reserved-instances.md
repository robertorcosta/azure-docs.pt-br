---
title: Pré-pago por Hosts Dedicados do Azure para economizar dinheiro
description: Saiba como comprar hosts dedicados do Azure instâncias reservadas para economizar em seus custos de computação.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207739"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Economize custos com uma instância reservada de hosts dedicados ao Azure

Quando você se compromete com uma instância reservada de Hosts Dedicados do Azure, você pode economizar dinheiro. O desconto de reserva é aplicado automaticamente ao número de hosts dedicados em execução que correspondem ao escopo e atributos de reserva. Você não precisa atribuir uma reserva a um host dedicado para obter os descontos. Uma compra de instância reservada cobre apenas a parte computacional do seu uso e inclui custos de licenciamento de software. Veja a [visão geral dos hosts dedicados do Azure para máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Determine o SKU do host dedicado direito antes de comprar


Antes de comprar uma reserva, você deve determinar qual host dedicado você precisa. Um SKU é definido para um host dedicado representando a série e o tipo vm. 

Comece repassando os tamanhos suportados para [a máquina virtual do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para identificar a série VM.

Em seguida, verifique se ele é suportado no Azure Dedicated Hosts. A página [de preços do Azure Dedicated Hosts](https://aka.ms/ADHPricing) tem a lista completa de SKUs hosts dedicados, suas informações de CPU e várias opções de preços (incluindo instâncias reservadas).

Você pode encontrar várias SKUs que lhe suportam séries VM (com diferentes tipos). Identifique o melhor SKU comparando a capacidade do host (número de vCPUs). Observe que você poderá aplicar sua reserva a vários hosts dedicados SKUs suportando a mesma série VM (por exemplo, DSv3_Type1 e DSv3_Type2), mas não em diferentes séries de VM (como DSv3 e ESv3).



## <a name="purchase-restriction-considerations"></a>Considerações sobre restrição de compra

As instâncias reservadas estão disponíveis para a maioria dos tamanhos de host dedicados, com algumas exceções.

Os descontos de reserva não se aplicam ao seguinte:

- **Nuvens** - As reservas não estão disponíveis para compra na Alemanha ou na China.

- **Cota insuficiente** - Uma reserva que seja escopo de uma única assinatura deve ter a cota de vCPU disponível na assinatura para a nova instância reservada. Por exemplo, se a assinatura-alvo tiver um limite de cota de 10 vCPUs para Série DSv3, então você não poderá comprar uma reserva dedicada a hosts que suportem esta série. O cheque de cotas para reservas inclui as VMs e hosts dedicados já implantados na assinatura. Você pode [criar solicitação](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) de aumento de cota para resolver esse problema.

- **Restrições de capacidade** - Em raras circunstâncias, o Azure limita a compra de novas reservas para subconjunto de SKUs de host dedicados, devido à baixa capacidade em uma região.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Você pode comprar uma instância reservada de uma instância de host dedicada do Azure no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Pague a reserva [antecipadamente ou com pagamentos mensais.](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations) Esses requisitos se aplicam à compra de uma instância de host dedicada reservada:

- Você deve estar em uma função de Proprietário para pelo menos uma assinatura da EA ou uma assinatura com uma taxa de pagamento.

- Para assinaturas EA, a opção **Adicionar instâncias** reservadas deve ser ativada no [portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você precisará ser um Administrador de EA da assinatura.

- Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar reservas.

Para comprara uma instância:

1. Faça login no  [ portal do Azure ](https://portal.azure.com/).

2. Selecione Todas as **reservas** **de serviços** \>.

3. Selecione **Adicionar** para comprar uma nova reserva e clique **em Hosts dedicados**.

4. Preencha os campos obrigatórios. Executando instâncias de hosts dedicados que correspondem aos atributos selecionados qualificar para obter o desconto de reserva. O número real de suas instâncias de Host dedicado que recebem o desconto depende do escopo e da quantidade selecionados.

Se você tiver um contrato com a EA, você pode usar a **opção** Adicionar mais para adicionar rapidamente instâncias adicionais. A opção não está disponível para outros tipos de assinatura.

| **Campo**           | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription        | A assinatura usada para pagar pela reserva. A forma de pagamento na assinatura é cobrada dos custos da reserva. O tipo de assinatura deve ser um contrato corporativo (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Contrato de Cliente da Microsoft ou uma assinatura individual com taxas de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os encargos são deduzidos do saldo de compromisso monetário, se disponíveis, ou cobrados como excesso de tempo. Para uma assinatura com taxas de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na assinatura. |
| Escopo               | O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Região              | A região do Azure que é coberta pela reserva.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Tamanho do host dedicado | O tamanho das instâncias de host dedicado.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Termo                | Um ano ou três anos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Quantidade            | O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias de Host dedicadas em execução que podem obter o desconto de faturamento.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Escopo de grupo de recursos únicos** — Aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.

- **Escopo de assinatura única** — Aplica o desconto de reserva aos recursos correspondentes na assinatura selecionada.

- **Escopo compartilhado** — Aplica o desconto de reserva para recursos correspondentes em assinaturas elegíveis que estão no contexto de faturamento. Para os clientes da EA, o contexto de faturamento é a inscrição. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.

## <a name="usage-data-and-reservation-utilization"></a>Dados de uso e utilização de reservas

Seus dados de uso têm um preço efetivo de zero para o uso, que obtém um desconto de reserva. Você pode ver qual instância vm recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de uso, consulte [Entenda o uso da reserva do Azure para sua inscrição](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) enterprise se você for um cliente Da EA. Se você tiver uma assinatura individual, consulte [Entender o uso da reserva do Azure para sua assinatura Pay-As-You-Go](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

É possível realizar os seguintes tipos de alterações em uma reserva após a compra:

- Atualizar o escopo de reserva

- Flexibilidade de tamanho de instância (se aplicável)

- Propriedade

Você também pode dividir uma reserva em pedaços menores e mesclar reservas já divididas. Nenhuma das alterações causa uma nova transação comercial ou altera a data final da reserva.

Você não pode fazer os seguintes tipos de alterações após a compra, diretamente:

- Região de uma reserva existente

- SKU

- Quantidade

- Duration

No entanto, você pode *trocar* uma reserva se quiser fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, consulte [trocas de autoatendimento e reembolsos para reservas do Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

Para saber como gerenciar uma reserva, consulte [Gerenciar reservas do Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance).

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Usando hosts dedicados ao Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Preços de hosts dedicados](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gerenciar reservas no Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Entender como o desconto de reserva é aplicado](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [Noções básicas sobre o uso de reserva para uma assinatura com taxas pagas conforme o uso](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Entender o uso de reserva para seu registro de empresa](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Custos de software do Windows não incluídos com reservas](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)


