---
title: Trocas e reembolsos realizados via autoatendimento nas Reservas do Azure
description: Saiba como você pode trocar ou reembolsar as Reservas do Azure.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 393db5d2e14e047ade04e0b688582e272c6ca44f
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200428"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Trocas e reembolsos realizados via autoatendimento nas Reservas do Azure

As Reservas do Azure fornecem flexibilidade para ajudar a atender às suas necessidades em constante evolução. Você pode trocar uma reserva por outra do mesmo tipo. Você também poderá pedir reembolso de uma reserva, de até USD 50.000 por ano, se não precisar mais dela. O limite máximo do reembolso se aplica a todas as reservas no escopo do seu contrato com a Microsoft.

Os recursos de cancelamento e troca de autoatendimento não estão disponíveis para clientes do US Government Enterprise Agreement. Há suporte a outros tipos de assinatura do governo dos EUA, incluindo o Pagamento Conforme o Uso e a CSP.

Você precisa ter acesso de proprietário ao Pedido de Reserva para trocar ou reembolsar uma reserva existente.

## <a name="exchange-an-existing-reserved-instance"></a>Trocar uma instância reservada existente

Você pode trocar sua reserva executando três etapas rápidas no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selecione as reservas que deseja reembolsar e selecione **Trocar**.  
    ![Exemplo de imagem mostrando reservas a serem devolvidas](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)
2. Selecione o produto de VM que você deseja comprar e digite uma quantidade. Verifique se o novo total de compra é maior que o total de devolução. [Determine o tamanho correto antes da compra](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Exemplo de imagem mostrando o produto da VM a ser comprado com uma troca](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)
3. Examine e conclua a transação.  
    ![Exemplo de imagem mostrando o produto da VM a ser comprado com uma troca, concluindo a devolução](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)

Para reembolsar uma reserva, acesse **Detalhes da Reserva** e selecione **Reembolsar**.

## <a name="how-transactions-are-processed"></a>Como as transações são processadas

Primeiro, a Microsoft cancela a reserva existente e reembolsa o valor proporcional àquela reserva. Caso haja uma troca, a nova compra será processada. A Microsoft processa os reembolsos por meio de um dos seguintes métodos, dependendo do tipo de conta e da forma de pagamento:

### <a name="enterprise-agreement-customers"></a>Clientes do Contrato Enterprise

Adiciona-se dinheiro ao compromisso monetário para trocas e reembolsos caso a compra original tenha sido feita usando tal. Toda fatura excedente desde as compras originais será reaberta e reclassificada para garantir que o compromisso monetário seja usado. Se o termo de compromisso monetário que usa a reserva comprada não estiver mais ativo, o crédito será adicionado ao seu atual termo de compromisso monetário do Contrato Enterprise atual. O crédito é válido por 90 dias a partir da data do reembolso. Créditos não utilizados expiram no final dos 90 dias.

Se a compra original foi feita como um excedente, a Microsoft emite um memorando de crédito.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Pagamentos de fatura pagos conforme o uso e programa CSP

A fatura de compra da reserva original é cancelada e uma nova fatura é criada para o reembolso. Para trocas, a nova fatura mostra o reembolso e a nova compra. O valor do reembolso é ajustado em relação à compra. Se você reembolsou apenas uma reserva, o valor proporcional permanece com a Microsoft e é ajustado em uma compra futura de reserva.

### <a name="pay-as-you-go-credit-card-customers"></a>Clientes de cartão de crédito pago conforme o uso

A fatura original é cancelada e uma nova fatura é gerada. O dinheiro é reembolsado no cartão de crédito que foi usado para realizar a compra original. Caso tenha mudado de cartão, [entre em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Políticas de cancelamento, troca e reembolso

O Azure tem as políticas de cancelamento, troca e reembolso a seguir.

**Políticas de troca**

- Você pode devolver várias reservas existentes a fim de comprar uma nova reserva do mesmo tipo. Não é permitido trocar reservas de um tipo por outro. Por exemplo, você não pode devolver uma reserva de VM para comprar uma reserva de SQL.
- Somente os proprietários de reserva podem processar uma troca. [Saiba como adicionar ou alterar os usuários que podem gerenciar uma reserva](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Uma troca é processada como um reembolso seguido de uma nova compra – diferentes transações são criadas para o cancelamento e a nova compra. O valor proporcional da reserva que você troca é reembolsado. O valor integral é cobrado pela nova compra. O valor proporcional da reserva é o valor residual diário proporcional da reserva que está sendo devolvida.
- Você pode trocar ou reembolsar reservas mesmo que o Contrato Enterprise usado para comprar a reserva esteja expirado e tenha sido renovado como um novo contrato.
- Você pode alterar a propriedade de uma reserva, como família, série, versão, SKU, região, quantidade e prazo com uma troca.
- O novo total de compra deve ser maior ou igual ao valor devolvido.
- A nova reserva comprada como parte de uma troca tem um novo prazo, que começa a contar desde o momento da efetivação da troca.
- Não há penalidades ou limites anuais para trocas.

**Políticas de reembolso**
- Poderá haver um valor de rescisão antecipado de 12% para cancelamentos no futuro. No momento, não estamos cobrando essa penalidade.
- O valor total de reembolso não pode exceder US$ 50.000 em uma janela de 12 meses consecutivos.
- O cálculo dos reembolsos é baseado no valor mais baixo entre o preço de compra e o preço atual da reserva.
- Somente os proprietários de pedidos de reserva podem processar um reembolso. [Saiba como adicionar ou alterar os usuários que podem gerenciar uma reserva](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Trocar armazenamento não Premium por armazenamento Premium

Você pode trocar uma reserva comprada para um tamanho de VM que não é compatível com o armazenamento Premium por um tamanho de VM correspondente que é compatível. Por exemplo, uma _F1_ por uma _F1s_. Para fazer a troca, acesse os Detalhes da Reserva e selecione **Trocar**. A troca não redefine o prazo da instância reservada, nem cria uma nova transação.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
    - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
    - [Gerenciar Reservas no Azure](manage-reserved-vm-instance.md)
    - [Entender como o desconto de reserva é aplicado](../manage/understand-vm-reservation-charges.md)
    - [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)
