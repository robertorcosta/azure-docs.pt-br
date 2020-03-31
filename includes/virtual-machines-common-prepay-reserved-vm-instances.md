---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371659"
---
Quando você se compromete com uma instância VM reservada do Azure, você pode economizar dinheiro. O desconto da reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao escopo e atributos da reserva. Você não precisa atribuir uma reserva a uma máquina virtual para obter os descontos. Uma compra de instância reservada cobre apenas a parte computacional do uso da VM. Para VMs do Windows, o medidor de uso é dividido em dois metros separados. Há um medidor de cálculo, que é o mesmo do medidor Linux, e um medidor IP do Windows. Os encargos que você vê quando você faz a compra são apenas para os custos de computação. As taxas não incluem os custos do software do Windows. Para obter mais informações sobre os custos de software, consulte [os custos de software não incluídos nas instâncias de VM reservadas do Azure](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinar o tamanho correto da VM antes de comprar

Antes de comprar uma reserva, você deve determinar o tamanho da VM que você precisa. As seções a seguir ajudarão você a determinar o tamanho certo da VM.

### <a name="use-reservation-recommendations"></a>Use recomendações de reserva

Você pode usar recomendações de reserva para ajudar a determinar as reservas que você deve comprar.

- Recomendações de compra e quantidade recomendada são mostradas quando você compra uma instância reservada de VM no portal Azure.
- O Azure Advisor fornece recomendações de compra para assinaturas individuais.  
- Você pode usar as APIs para obter recomendações de compra tanto para escopo compartilhado quanto para escopo de assinatura única. Para obter mais informações, consulte [APIs de recomendação de compra de instância reservada para clientes corporativos.](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)
- Para clientes de Acordo de Cliente S(EA) e Microsoft Customer Agreement (MCA), as recomendações de compra para escopos de assinatura compartilhados e únicos estão disponíveis com o [pacote de conteúdo do Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Serviços que têm descontos de reserva de VM

Suas reservas de VM podem se aplicar ao uso de VM emitido de vários serviços – não só para suas implantações de VM. Os recursos que têm descontos de reserva mudam dependendo da configuração de flexibilidade de tamanho da instância.

#### <a name="instance-size-flexibility-setting"></a>Configuração de flexibilidade de tamanho da instância

A configuração de flexibilidade de tamanho da instância determina quais serviços têm os descontos de instância reservada.

Se a configuração estiver ativada ou desativada, os descontos de reserva se aplicarão automaticamente a qualquer uso da VM correspondente quando *ConsumedService* for `Microsoft.Compute`. Portanto, verifique os dados de uso para o valor *ConsumedService*. Alguns exemplos incluem:

- Máquinas virtuais
- conjuntos de escala de máquina virtual
- Serviço de contêiner
- Implantações do Lote do Azure (no modo de assinaturas do usuário)
- AKS (Serviço de Kubernetes do Azure)
- Service Fabric

Quando a configuração estiver ativada, os descontos de reserva se aplicarão automaticamente ao uso de VM correspondente quando *ConsumedService* for qualquer um dos seguintes itens:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Verifique o valor *ConsumedService* em seus dados de uso para determinar se o uso está qualificado para descontos de reserva.

Para obter mais informações sobre a flexibilidade de tamanho da instância, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analise suas informações de uso

Analise suas informações de uso para ajudar a determinar quais reservas você deve comprar. Os dados de uso estão disponíveis no arquivo de uso e NAS. Use-os juntos para determinar qual reserva comprar. Verifique se há instâncias de VM que têm alto uso diariamente para determinar a quantidade de reservas para comprar. Evite `Meter` a subcategoria e `Product` os campos nos dados de uso. Eles não distinguem entre tamanhos de VM que usam armazenamento premium. Se você usar esses campos para determinar o tamanho da VM para a compra de reservas, você pode comprar o tamanho errado. Então você não terá o desconto de reserva que você espera. Em vez disso, consulte o `AdditionalInfo` campo em seu arquivo de uso ou API de uso para determinar o tamanho correto da VM.

Seu arquivo de uso mostra suas taxas por período de cobrança e uso diário. Para obter informações sobre como baixar seu arquivo de uso, consulte [Exibir e baixar o uso e as taxas do Azure](../articles/cost-management-billing/understand/download-azure-daily-usage.md). Em seguida, usando as informações do arquivo de uso, você pode [determinar qual reserva comprar](../articles/cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Considerações sobre restrição de compra

As instâncias vm reservadas estão disponíveis para a maioria dos tamanhos de VM, com algumas exceções. Os descontos de reserva não se aplicam às seguintes VMs:

- **Série VM** - Série A, Série Av2 ou Série G.

- **Pré-visualização ou VMs promocionais** - Qualquer série de VM ou tamanho que esteja em pré-visualização ou use medidor promocional.

- **Nuvens** - As reservas não estão disponíveis para compra na Alemanha ou na China.

- **Cota insuficiente** - Uma reserva que seja escopo de uma única assinatura deve ter cota de vCPU disponível na assinatura do novo RI. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a Série D, você não poderá comprar uma reserva para 11 instâncias Standard_D1. A verificação de cota para reservas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para o D-Series e tiver duas instâncias standard_D1 implantadas, você poderá comprar uma reserva para 10 instâncias standard_D1 nessa assinatura. Você pode [criar solicitação de aumento de cotação](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) para resolver esse problema.

- **Restrições de capacidade** - Em raras circunstâncias, o Azure limita a compra de novas reservas para subconjunto de tamanhos de VM, devido à baixa capacidade em uma região.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma Instância de VM Reservada

Você pode comprar uma instância de VM reservada no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).
Esses requisitos se aplicam à compra de uma instância de VM reservada:

- Você deve estar em uma função de Proprietário para pelo menos uma assinatura da EA ou uma assinatura com uma taxa de pagamento.
- Para assinaturas EA, a opção **Adicionar instâncias reservadas** deve ser ativada no [portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você precisará ser um Administrador de EA da assinatura.
- Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar reservas.

Para comprara uma instância:

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione Todas as**reservas** **de serviços** > .
1. Selecione **Adicionar** para comprar uma nova reserva e, em seguida, clique **em Máquina Virtual**.
1. Preencha os campos obrigatórios. As instâncias de VM em execução que correspondem com os atributos que você selecionar, ficam qualificados para obter o desconto de reserva. O número real de suas instâncias VM que obtêm o desconto depende do escopo e da quantidade selecionada.

Se você tiver um contrato com a EA, você pode usar a **opção Adicionar mais** para adicionar rapidamente instâncias adicionais. A opção não está disponível para outros tipos de assinatura.


| Campo      | Descrição|
|------------|--------------|
|Subscription|A assinatura usada para pagar pela reserva. A forma de pagamento na assinatura é cobrada dos custos da reserva. O tipo de assinatura deve ser um contrato corporativo (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Contrato de Cliente da Microsoft ou uma assinatura individual com taxas de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os encargos são deduzidos do saldo de compromisso monetário, se disponíveis, ou cobrados como excesso de tempo. Para uma assinatura com taxas de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na assinatura.|    
|Escopo       |O escopo da reserva pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>**Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.</li><li>**Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.</li><li>**Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para os clientes da EA, o contexto de faturamento é a inscrição. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.</li></ul>|
|Região    |A região do Azure que é coberta pela reserva.|    
|Tamanho da VM     |O tamanho das instâncias de VM.|
|Otimizar para     |A flexibilidade do tamanho da instância vM é selecionada por padrão. Clique **em configurações avançadas** para alterar o valor de flexibilidade de tamanho da ocorrência para aplicar o desconto de reserva a outras VMs no mesmo [grupo de tamanho vm](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). A prioridade da capacidade prioriza a capacidade de data center para suas implantações. Ele oferece confiança adicional na sua capacidade de iniciar as instâncias de VM quando você precisa delas. A prioridade de capacidade está disponível apenas quando o escopo de reserva é uma assinatura única. |
|Termo        |Um ano ou três anos.|
|Quantidade    |O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias de VM que podem obter o desconto de cobrança. Por exemplo, se você estiver executando 10 Standard_D2 VMs no Leste dos EUA, então você especificaria a quantidade como 10 para maximizar o benefício para todas as VMs em execução. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dados de uso e utilização de reservas

Seus dados de uso têm um preço efetivo de zero para o uso, que obtém um desconto de reserva. Você pode ver qual instância vm recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de uso, consulte [Entenda o uso da reserva do Azure para sua inscrição enterprise](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) se você for um cliente Da EA. Se você tiver uma assinatura individual, consulte [Entender o uso da reserva do Azure para sua assinatura Pay-As-You-Go](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md).

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

No entanto, você pode *trocar* uma reserva se quiser fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
    - [O que são Reservas do Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Gerenciar reservas no Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Entender como o desconto de reserva é aplicado](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Noções básicas sobre o uso de reserva para uma assinatura com taxas pagas conforme o uso](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não incluídos com reservas](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)
