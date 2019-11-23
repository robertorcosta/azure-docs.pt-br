---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: f583796fc353852ef3898e28fa96524e08cfb4ad
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414633"
---
When you commit to an Azure reserved VM instance you can save money. O desconto da reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao escopo e atributos da reserva. You don't need to assign a reservation to a virtual machine to get the discounts. A reserved instance purchase covers only the compute part of your VM usage. For Windows VMs, the usage meter is split into two separate meters. There's a compute meter, which is same as the Linux meter, and a Windows IP meter. The charges that you see when you make the purchase are only for the compute costs. Charges don't include Windows software costs. For more information about software costs, see [Software costs not included with Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinar o tamanho correto da VM antes de comprar

Before you buy a reservation, you should determine the size of the VM that you need. The following sections will help you determine the right VM size.

### <a name="use-reservation-recommendations"></a>Use reservation recommendations

You can use reservation recommendations to help determine the reservations you should purchase.

- Purchase recommendations and recommended quantity are show when you purchase a VM reserved instance in the Azure portal.
- Azure Advisor provides purchase recommendations for individual subscriptions.  
- You can use the APIs to get purchase recommendations for both shared scope and single subscription scope. For more information, see [Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- For Enterprise Agreement (EA) and Microsoft Customer Agreement (MCA) customers, purchase recommendations for shared and single subscription scopes are available with the [Azure Consumption Insights Power BI content pack](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Serviços que têm descontos de reserva de VM

Suas reservas de VM podem se aplicar ao uso de VM emitido de vários serviços – não só para suas implantações de VM. Os recursos que têm descontos de reserva mudam dependendo da configuração de flexibilidade de tamanho da instância.

#### <a name="instance-size-flexibility-setting"></a>Configuração de flexibilidade de tamanho da instância

A configuração de flexibilidade de tamanho da instância determina quais serviços têm os descontos de instância reservada.

Se a configuração estiver ativada ou desativada, os descontos de reserva se aplicarão automaticamente a qualquer uso da VM correspondente quando *ConsumedService* for `Microsoft.Compute`. Portanto, verifique os dados de uso para o valor *ConsumedService*. Alguns exemplos incluem:

- Máquinas virtuais
- Conjuntos de dimensionamento de máquinas virtuais
- Serviço de contêiner
- Implantações do Lote do Azure (no modo de assinaturas do usuário)
- AKS (Serviço de Kubernetes do Azure)
- Malha de Serviço

Quando a configuração estiver ativada, os descontos de reserva se aplicarão automaticamente ao uso de VM correspondente quando *ConsumedService* for qualquer um dos seguintes itens:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Verifique o valor *ConsumedService* em seus dados de uso para determinar se o uso está qualificado para descontos de reserva.

Para obter mais informações sobre a flexibilidade de tamanho da instância, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analyze your usage information
Analyze your usage information to help determine which reservations you should purchase.

Usage data is available in the usage file and APIs. Use them together to determine which reservation to purchase. Check for VM instances that have high usage on daily basis to determine the quantity of reservations to purchase.

Avoid the `Meter` subcategory and `Product` fields in usage data. They don't distinguish between VM sizes that use premium storage. If you use these fields to determine the VM size for reservation purchase, you may buy the wrong size. Then you won't get the reservation discount you expect. Instead, refer to the `AdditionalInfo` field in your usage file or usage API to determine the correct VM size.

### <a name="purchase-restriction-considerations"></a>Purchase restriction considerations

Reserved VM Instances are available for most VM sizes with some exceptions. Reservation discounts don't apply for the following VMs:

- **VM series** - A-series, Av2-series, or G-series.

- **Preview or Promo VMs** - Any VM-series or size that is in preview or uses promotional meter.

- **Clouds** - Reservations aren't available for purchase in Germany or China regions.

- **Insufficient quota** - A reservation that is scoped to a single subscription must have vCPU quota available in the subscription for the new RI. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a Série D, você não poderá comprar uma reserva para 11 instâncias Standard_D1. A verificação de cota para reservas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para o D-Series e tiver duas instâncias standard_D1 implantadas, você poderá comprar uma reserva para 10 instâncias standard_D1 nessa assinatura. You can [create quote increase request](../articles/azure-supportability/resource-manager-core-quotas-request.md) to resolve this issue.

- **Capacity restrictions** - In rare circumstances, Azure limits the purchase of new reservations for subset of VM sizes, because of low capacity in a region.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma Instância de VM Reservada

You can buy a reserved VM instance in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](../articles/billing/billing-monthly-payments-reservations.md).
These requirements apply to buying a reserved VM instance:

- You must be in an Owner role for at least one EA subscription or a subscription with a pay-as-you-go rate.
- For EA subscriptions, the **Add Reserved Instances** option must be enabled in the [EA portal](https://ea.azure.com/). Or, if that setting is disabled, you must be an EA Admin for the subscription.
- Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar reservas.

Para comprara uma instância:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** > **Reservas**.
1. Select **Add** to purchase a new reservation and then click **Virtual machine**.
1. Enter required fields. As instâncias de VM em execução que correspondem com os atributos que você selecionar, ficam qualificados para obter o desconto de reserva. O número real de suas instâncias VM que obtêm o desconto depende do escopo e da quantidade selecionada.

If you have an EA agreement, you can use the **Add more option** to quickly add additional instances. The option isn't available for other subscription types.


| Campo      | Descrição|
|------------|--------------|
|Subscription|A assinatura usada para pagar pela reserva. The payment method on the subscription is charged the costs for the reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or Microsoft Customer Agreement or an individual subscription with pay-as-you-go rates (offer numbers: MS-AZR-0003P or MS-AZR-0023P). The charges are deducted from the monetary commitment balance, if available, or charged as overage. For a subscription with pay-as-you-go rates, the charges are billed to the credit card or invoice payment method on the subscription.|    
|Escopo       |O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>**Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.</li><li>**Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.</li><li>**Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. For EA customers, the billing context is the enrollment. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.</li></ul>|
|Região    |A região do Azure que é coberta pela reserva.|    
|Tamanho da VM     |O tamanho das instâncias de VM.|
|Otimizar para     |VM instance size flexibility is selected by default. Click **Advanced settings** to change the instance size flexibility value to apply the reservation discount to other VMs in the same [VM size group](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). A prioridade da capacidade prioriza a capacidade de data center para suas implantações. It offers additional confidence in your ability to launch the VM instances when you need them. A prioridade de capacidade está disponível apenas quando o escopo de reserva é uma assinatura única. |
|Prazo        |Um ano ou três anos.|
|Quantidade    |O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias de VM que podem obter o desconto de cobrança. For example, if you are running 10 Standard_D2 VMs in the East US, then you would specify quantity as 10 to maximize the benefit for all running VMs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Usage data and reservation utilization

Seus dados de uso têm um preço efetivo de zero para o uso, que obtém um desconto de reserva. You can see which VM instance received the reservation discount for each reservation.

For more information about how reservation discounts appear in usage data, see [Understand Azure reservation usage for your Enterprise enrollment](../articles/billing/billing-understand-reserved-instance-usage-ea.md) if you are an EA customer. If you have an individual subscription, see [Understand Azure reservation usage for your Pay-As-You-Go subscription](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Change a reservation after purchase

É possível realizar os seguintes tipos de alterações em uma reserva após a compra:

- Atualizar o escopo de reserva
- Instance size flexibility (if applicable)
- Ownership

You can also split a reservation into smaller chunks and merge already split reservations. None of the changes cause a new commercial transaction or change the end date of the reservation.

You can't make the following types of changes after purchase, directly:

- An existing reservation’s region
- SKU
- Quantidade
- Duration

However, you can *exchange* a reservation if you want to make changes.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contate-nos.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximos passos

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:
    - [Quais são as reservas do Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gerenciar reservas no Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Entender como o desconto de reserva é aplicado](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Noções básicas sobre o uso de reserva para uma assinatura com taxas pagas conforme o uso](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Os custos de software do Windows não incluídos com instâncias reservadas](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center.](https://docs.microsoft.com/partner-center/azure-reservations)
