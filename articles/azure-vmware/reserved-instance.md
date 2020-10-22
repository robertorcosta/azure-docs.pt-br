---
title: Economize custos com a instância reservada da solução Azure VMware
description: Saiba como comprar uma instância reservada para a solução do Azure VMware.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: bac2497c637a301c7ce8cbc44fc6945c3ef43b06
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370671"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Economize custos com a solução Azure VMware

Quando você se compromete com uma instância reservada da [solução do Azure VMware](introduction.md), economiza dinheiro. O desconto de reserva é aplicado automaticamente aos hosts da solução do Azure VMware em execução que correspondem ao escopo e aos atributos de reserva. Você não precisa atribuir uma reserva a um host dedicado para obter os descontos. Uma compra de instância reservada abrange apenas a parte de computação do seu uso e inclui os custos de licenciamento de software. 


## <a name="purchase-restriction-considerations"></a>Considerações sobre a restrição de compra

As instâncias reservadas estão disponíveis com algumas exceções.

-   **Nuvens** – as reservas estão disponíveis apenas nas regiões listadas na página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Cota insuficiente** -uma reserva com escopo para uma assinatura única/compartilhada deve ter a cota de hosts disponível na assinatura para a nova instância reservada. Você pode [criar uma solicitação de aumento de cota](enable-azure-vmware-solution.md) para resolver esse problema.

-   **Qualificação da oferta**-você precisará de um [ea (Enterprise Agreement do Azure)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.

-   **Restrições de capacidade** -em raras circunstâncias, o Azure limita a compra de novas reservas para SKUs do host da solução Azure VMware devido à baixa capacidade em uma região.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Você pode comprar uma instância reservada de uma instância de host de solução do Azure VMware no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md).

Esses requisitos se aplicam à compra de uma instância de host dedicada reservada:

-   Você deve estar em uma função de proprietário para pelo menos uma assinatura de EA ou uma assinatura com uma taxa pré-paga.

-   Para assinaturas EA, a opção **adicionar instâncias reservadas** deve ser habilitada no [portal de ea](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você precisará ser um Administrador de EA da assinatura.

Para comprar uma instância:

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Todos os serviços** > **Reservas**.

3. Selecione **Adicionar** para comprar uma nova reserva e, em seguida, selecione **solução Azure VMware**.

4. Insira os campos obrigatórios. Executando os hosts da solução Azure VMware que correspondem aos atributos, você seleciona qualificar para obter o desconto de reserva. O número real de seus hosts de solução do Azure VMware que obtém o desconto dependendo do escopo e da quantidade selecionada.

   Se você tiver um contrato EA, poderá usar a **opção Adicionar mais** para adicionar instâncias adicionais rapidamente. A opção não está disponível para outros tipos de assinatura.

   | Campo        |  Descrição |
   | ------------ | ------------ |
   | Subscription | A assinatura usada para pagar pela reserva. Os custos da reserva são cobrados segundo a forma de pagamento da assinatura. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou o contrato de cliente da Microsoft ou uma assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os preços são deduzidos do saldo do compromisso monetário, se disponível, ou são cobrados como excedente. Para uma assinatura com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura. |
   | Escopo        | O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar:<br><ul><li><b>Escopo do grupo de recursos único — aplica o desconto de reserva aos recursos correspondentes somente no grupo de recursos selecionado.</li><li><b>Escopo de assinatura única — aplica o desconto de reserva aos recursos de correspondência na assinatura selecionada.</li><li><b>Escopo compartilhado — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do EA, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.</li></ul>       |
   | Região       | A região do Azure que é coberta pela reserva.   |
   | Tamanho do host    | AV36    |
   | Termo         | Um ano ou três anos.  |
   | Quantidade     | O número de instâncias sendo compradas na reserva. A quantidade é o número de hosts da solução do Azure VMware em execução que podem obter o desconto de cobrança.    |

## <a name="usage-data-and-reservation-usage"></a>Dados de uso e uso de reserva

Seu uso que obtém um desconto de reserva tem um preço efetivo de zero. Você pode ver qual instância de solução do Azure VMware recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de uso:

- Para clientes do EA, consulte [entender o uso de reserva do Azure para seu registro corporativo](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Para assinaturas individuais, consulte [entender o uso de reserva do Azure para sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

É possível realizar os seguintes tipos de alterações em uma reserva após a compra:

-   Atualizar o escopo de reserva

-   Flexibilidade de tamanho de instância (se aplicável)

-   Propriedade

Você também pode dividir uma reserva em partes menores ou reservas de mesclagem. Nenhuma das alterações causa uma nova transação comercial ou alterar a data de término da reserva.

>[!NOTE]
>Depois de adquirir sua reserva, você não poderá fazer os seguintes tipos de alterações diretamente:
>
> - Uma região de reserva existente
> - SKU
> - Quantidade
> - Duration
>
>No entanto, você pode *trocar* uma reserva se desejar fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).