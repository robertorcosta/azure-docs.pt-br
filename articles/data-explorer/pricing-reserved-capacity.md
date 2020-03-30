---
title: Pré-pago para marcação do Azure Data Explorer para economizar dinheiro
description: Saiba como comprar o Azure Data Explorer, capacidade reservada para economizar nos custos do Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969280"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Pré-pago para unidades de marcação do Azure Data Explorer com capacidade reservada do Azure Data Explorer

Economize dinheiro com o Azure Data Explorer pagando antecipadamente pelas unidades de marcação em comparação com os preços de pagamento. Com a capacidade reservada do Azure Data Explorer, você se compromete com o uso do Azure Data Explorer por um período de um ou três anos para obter um desconto significativo nos custos de marcação do Azure Data Explorer. Para comprar a capacidade reservada do Azure Data Explorer, você só precisa especificar o termo, ele se aplicará a todas as implantações do Azure Data Explorer em todas as regiões.

Ao comprar uma reserva, você está pré-pagando os custos de marcação por um período de um ou três anos. Assim que você compra uma reserva, as taxas de marcação do Azure Data Explorer que correspondem aos atributos de reserva não são mais cobradas nas taxas de pagamento à medida que você vai. Os clusters do Azure Data Explorer que já estão em execução ou os recém-implantados receberão automaticamente o benefício. Essa reserva não cobre cargas de computação, rede ou armazenamento associadas aos clusters. A capacidade reservada para esses recursos precisa ser adquirida separadamente. No final do prazo de reserva, o benefício de faturamento expira e as unidades de marcação do Azure Data Explorer são cobradas pelo preço de pagamento à medida que você vai. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a página de preços do [Azure Data Explorer](https://azure.microsoft.com/pricing/details/data-explorer/).

Você pode comprar a capacidade reservada do Azure Data Explorer no [portal Azure](https://portal.azure.com). Para comprar a capacidade reservada do Azure Data Explorer:

* Você deve ser o proprietário de pelo menos uma assinatura Enterprise ou Pay-As-You-Go.
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Alternativamente, se essa configuração estiver desativada, você deve ser um administrador da EA na assinatura.
* Para o programa CSP (Cloud Solution Provider, provedor de soluções em nuvem), apenas os agentes de administradores ou agentes de vendas podem comprar a capacidade reservada do Azure Data Explorer.

Para obter detalhes sobre como clientes corporativos e clientes pay-as-you-go são cobrados para compras de reservas, consulte:
* [Entenda o uso de reserva do Azure para sua inscrição na empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Entenda o uso da reserva do Azure para sua assinatura Pay-As-You-Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Determine o uso de marcação certa antes da compra

O tamanho da reserva deve ser baseado no número total de unidades de marcação do Azure Data Explorer usadas pelos clusters Azure Data Explorer existentes ou em breve. O número de unidades de marcação é igual ao número de núcleos de cluster do motor Azure Data Explorer em produção (sem incluir o dev/teste SKU). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Comprar capacidade reservada do Azure Data Explorer

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** > **que as reservas** > **comprarem agora**. Selecione **Adicionar**
1. No painel **Selecionar tipo de produto,** selecione **o Azure Data Explorer** para comprar uma nova reserva para unidades de marcação do Azure Data Explorer. 
1. Selecione **Comprar**
1. Preencha os campos obrigatórios. 

    ![Página de compra](media/pricing-reserved-capacity/purchase-page.png)

1. Revise o custo da reserva de capacidade reservada do Azure Data Explorer na seção **Custos.**
1. Selecione **Comprar**.
1. Selecione **Exibir essa Reserva** para ver o status de sua compra.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Se você precisar cancelar sua reserva de capacidade reservada do Azure Data Explorer, pode haver uma taxa de rescisão antecipada de 12%. Os reembolsos são baseados no menor preço do seu preço de compra ou no preço atual da reserva. Os reembolsos são limitados a US$ 50.000 por ano. O reembolso que você recebe é o saldo proporcional restante menos o valor da tarifa de rescisão antecipada de 12%. Para solicitar um cancelamento, vá até a reserva no portal do Azure e selecione **Reembolso** para criar uma solicitação de suporte.

Se você precisar alterar sua reserva de capacidade reservada do Azure Data Explorer para outro termo, você pode trocá-la por outra reserva que seja de igual ou maior valor. A data de início do prazo da nova reserva não é a mesma da reserva trocada. O prazo de 1 ou 3 anos começa quando você cria a nova reserva. Para solicitar uma troca, vá até a reserva no portal do Azure e selecione **Trocar** para criar uma solicitação de suporte.

Para obter mais informações sobre como trocar ou reembolsar reservas, consulte [Reservas e reembolsos](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="manage-your-reserved-capacity-reservation"></a>Gerencie sua reserva de capacidade reservada

O desconto de reserva de unidades de marcação do Azure Data Explorer é aplicado automaticamente ao número de unidades de marcação que correspondem ao escopo e atributos de reserva de capacidade reservada do Azure Data Explorer. 


> [!NOTE]
> * Você pode atualizar o escopo da reserva de capacidade reservada do Azure Data Explorer através do [portal Azure,](https://portal.azure.com)PowerShell, CLI ou através da API.
> * Para saber como gerenciar a reserva de capacidade reservada do Azure Data Explorer, consulte gerenciar a [capacidade reservada do Azure Data Explorer](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

* [O que são Reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Gerenciar reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Compreender o desconto de Reservas do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Entender o uso de reserva para a sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [Entender o uso de reserva para seu registro de empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
