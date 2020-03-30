---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590768"
---
Economize no uso do armazenamento de disco do Azure com capacidade reservada. As reservas de armazenamento em disco do Azure combinadas com as instâncias de máquina virtual reservadas do Azure permitem reduzir os custos totais da máquina virtual (VM). O desconto de reserva é aplicado automaticamente aos discos correspondentes no escopo de reserva selecionado. Por causa deste aplicativo automático, você não precisa atribuir uma reserva a um disco gerenciado para obter os descontos.

Os descontos são aplicados por hora, dependendo do uso do disco. A capacidade reservada não utilizada não é maior. Os descontos de reserva do Azure Disk Storage não se aplicam a discos não gerenciados, discos ultra ou consumo de bolhas de página.

## <a name="determine-your-storage-needs"></a>Determine suas necessidades de armazenamento

Antes de comprar uma reserva, determine suas necessidades de armazenamento. Atualmente, as reservas de armazenamento em disco do Azure estão disponíveis apenas para SSD SSD premium selecionado ssd skus selecionados. O SKU de um SSD premium determina o tamanho e o desempenho do disco.

Ao determinar suas necessidades de armazenamento, não pense em discos baseados apenas na capacidade. Por exemplo, você não pode ter uma reserva para um disco P40 e usá-lo para pagar por dois discos P30 menores. Ao comprar uma reserva, você está apenas comprando uma reserva para o número total de discos por SKU.

Uma reserva de disco é feita por disco SKU. Como resultado, o consumo de reserva é baseado na unidade do disco SKUs em vez do tamanho fornecido.

Por exemplo, suponha que você reserve um disco P40 que tenha 2 TiB de capacidade de armazenamento provisionado. Também suponha que você aloque apenas dois discos P30. A reserva P40 nesse caso não contabiliza o consumo de P30, e você paga a taxa de pagamento nos discos P30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considerações de compra

Recomendamos as seguintes práticas ao considerar a compra de reservas de disco:

- Analise suas informações de uso para ajudar a determinar quais reservas você deve comprar. Certifique-se de rastrear o uso em SKUs de disco em vez de capacidade de disco provisionada ou usada.
- Examine sua reserva de disco junto com sua reserva de VM. Recomendamos fazer reservas tanto para o uso de VM quanto para o uso de disco para a maior economia. Você pode começar com a determinação da reserva vm certa e, em seguida, avaliar a reserva de disco. Geralmente, você terá uma configuração padrão para cada uma de suas cargas de trabalho. Por exemplo, um servidor SQL Server pode ter dois discos de dados P40 e um disco do sistema operacional P30.
  
  Esse tipo de padrão pode ajudá-lo a determinar o valor reservado que você pode comprar. Essa abordagem pode simplificar o processo de avaliação e garantir que você tenha um plano alinhado tanto para sua VM quanto para discos. O plano contém considerações como assinaturas ou regiões.

## <a name="purchase-restrictions"></a>Restrições de compra

Os descontos de reserva estão atualmente indisponíveis para o seguinte:

- Discos não gerenciados ou bolhas de página.
- SSDs padrão ou unidades de disco rígido padrão (HDDs).
- SSD SKUs premium menores que P30: P1, P2, P3, P4, P6, P10, P15 e P20 SSD SKUs.
- Discos nas regiões do Azure Government, Azure Germany ou Azure China.

Em raras circunstâncias, o Azure limita a compra de novas reservas a um subconjunto de SKUs de disco devido à baixa capacidade em uma região.

## <a name="buy-a-disk-reservation"></a>Compre uma reserva de disco

Você pode comprar reservas de armazenamento de disco Azure através do [portal Azure](https://portal.azure.com/). Você pode pagar a reserva antecipadamente ou com pagamentos mensais. Para obter mais informações sobre compras com pagamentos mensais, consulte [Reservas de Compra com pagamentos mensais.](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)

Siga estas etapas para adquirir capacidade reservada:

1. Acesse o painel [de reservas de compras](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal Azure.

1. Selecione **Discos Gerenciados do Azure** para comprar uma reserva.

    ![Painel para compras de reservas](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Especifique os valores necessários descritos na tabela a seguir:

   |Elemento  |Descrição  |
   |---------|---------|
   |**Escopo**   |  Quantas assinaturas podem usar o benefício de faturamento associado à reserva. Esse valor também especifica como a reserva é aplicada a assinaturas específicas. <br/><br/> Se você selecionar **Compartilhado,** o desconto de reserva será aplicado à capacidade de armazenamento do Azure em cada assinatura dentro do seu contexto de faturamento. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes que pagam, o escopo compartilhado inclui todas as assinaturas individuais com taxas de pagamento criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **Assinatura única,** o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada. <br/><br/> Se você selecionar **Grupo de recursos individuais,** o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada e no grupo de recursos selecionado dessa assinatura. <br/><br/> Você pode alterar o escopo da reserva depois de comprar a reserva.  |
   |**Assinatura**  | A assinatura que você usa para pagar a reserva do Azure Storage. O método de pagamento na assinatura selecionada é usado na cobrança dos custos. A assinatura deve ser um dos seguintes tipos:<br/><ul><li> Contrato Empresarial (números de oferta MS-AZR-0017P e MS-AZR-0148P). Para uma assinatura Enterprise, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como excedente.</li><br/><li>Assinatura individual com taxas de pagamento (ofereça números MS-AZR-0003P e MS-AZR-0023P). Para uma assinatura individual com taxas de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na assinatura.</li></ul>    |
   | **Discos** | O SKU que você quer criar. |
   | **Região** | A região onde a reserva está em vigor. |
   | **Frequência de faturamento** | Quantas vezes a conta é cobrada pela reserva. As opções incluem **Mensal** e **Inicial**. |

    ![Painel para selecionar o produto que você deseja comprar.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Depois de especificar os valores da sua reserva, o portal Azure exibe o custo. O portal também mostra a porcentagem de desconto sobre o faturamento do pay-as-you-go. Selecione **Next** para continuar para o painel **de reservas de compra.**

1. No painel **de reservas de compra,** você pode nomear sua reserva e selecionar a quantidade total de reservas que deseja fazer. O número de mapas de reservas para o número de discos. Por exemplo, se você quiser reservar cem discos, digite o valor **quantidade** **100**.

1. Revise o custo total da reserva.

    ![O painel de reservas de compras](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Depois de comprar uma reserva, ela é aplicada automaticamente a quaisquer recursos de armazenamento de disco existentes que correspondam aos termos de reserva. Se você ainda não criou nenhum recurso de armazenamento de disco, a reserva se aplica sempre que você criar um recurso que corresponda aos termos de reserva. Em ambos os casos, o prazo de reserva começa imediatamente após uma compra bem sucedida.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Você pode cancelar, trocar ou reembolsar reservas dentro de certas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade de armazenamento de disco do Azure que você usa essa reserva é cobrada na taxa de pagamento à medida que você vai. As reservas não são renovadas automaticamente.

Você receberá uma notificação por e-mail 30 dias antes do vencimento da reserva e novamente na data de validade. Para continuar aproveitando a redução de custos que uma reserva proporciona, renove-a até a data de vencimento.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [O que são Reservas do Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Entenda como o desconto de reserva é aplicado ao Armazenamento em Disco do Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
