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
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847128"
---
Economize em seu uso de SSD (unidade de estado sólido) Premium com capacidade reservada, combinada com instâncias de máquina virtual reservadas você pode diminuir os custos totais da VM. O desconto de reserva é aplicado automaticamente aos discos correspondentes no escopo de reserva selecionado, você não precisa atribuir uma reserva a um disco gerenciado para obter os descontos. Os descontos são aplicados por hora no uso do disco e qualquer capacidade reservada não utilizada não é transferida. O desconto de reserva de disco gerenciado não se aplica a discos não gerenciados, ultra discos ou consumo de blob de páginas.

## <a name="determine-your-storage-needs"></a>Determinar suas necessidades de armazenamento

Antes de comprar uma reserva, você deve determinar quais são suas necessidades de armazenamento. Atualmente, a reserva de disco só está disponível para os SKUs selecionados do SSD Premium. A SKU de um SSD Premium determina o tamanho e o desempenho do disco. Ao determinar suas necessidades de armazenamento, não recomendamos pensar em discos assim como uma capacidade total, você não pode usar uma reserva para um disco maior (como um P40) e usá-lo para pagar por dois discos menores (p30). Ao comprar uma reserva, você está apenas comprando o número total de discos por SKU.

A reserva de disco é feita por SKU de disco, portanto, o consumo de reserva é baseado na unidade das SKUs de disco em vez do tamanho fornecido. Por exemplo, se você tiver reservado 1 P40 de 2 TiB de capacidade provisionada, mas apenas dois discos p30 alocados, o consumo de p30 não será contabilizado para a reserva de P40 e você pagará a taxa paga conforme o uso.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considerações de compra

Recomendamos as seguintes práticas recomendadas ao considerar a compra de reserva de disco:

- Analise suas informações de uso para ajudar a determinar quais reservas devem ser compradas. Certifique-se de que você está acompanhando o uso em SKUs de disco, em vez de provisionar ou usar a capacidade de disco. 
- Examine sua reserva de disco juntamente com sua reserva de VM. É altamente recomendável fazer reserva para a VM e o uso do disco para o máximo de salvamento. Você pode começar a determinar a reserva de VM correta e, em seguida, avaliar a reserva de disco de acordo. Em geral, você terá uma configuração padrão para cada uma de suas cargas de trabalho, por exemplo, um SQL Server pode ter dois discos de dados P40 e um disco de sistema operacional p30. Esse tipo de padrão pode ajudá-lo a determinar a quantidade de reservas que você pode comprar. Essa abordagem pode simplificar o processo de avaliação e também garantir que você tenha um plano alinhado para VM e discos em termos de assinaturas, regiões e outros. 

## <a name="purchase-restrictions"></a>Restrições de compra

Os descontos de reserva não estão disponíveis no momento para os seguintes discos:
- Discos não gerenciados/blobs de páginas
- HDD SSD Standard ou Standard
- SSD Premium SKUs menores que p30 – as reservas não estão disponíveis para SKUs p1/p2/p3/p4/P6/P10/P15/P20 SSD Premium.
- Nuvens – as reservas não estão disponíveis para compra nas regiões do Azure gov, Alemanha ou China.
- Restrições de capacidade-em raras circunstâncias, o Azure limita a compra de novas reservas para o subconjunto de SKUs de disco, devido à baixa capacidade em uma região.

## <a name="buy-a-disk-reservation"></a>Comprar uma reserva de disco

Você pode comprar reservas de disco do Azure por meio do [portal do Azure](https://portal.azure.com/). Você pode pagar pela reserva antecipada ou por pagamentos mensais. Para obter mais informações sobre como comprar com pagamentos mensais, consulte [reservas de compra com pagamentos mensais](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Siga estas etapas para comprar a capacidade reservada:

1. Navegue até a folha [reservas de compra](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal do Azure.
1. Selecione **Managed disks do Azure** para comprar uma reserva.

    ![disks-reserved-Purchase-Reservation. png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. Preencha os campos obrigatórios conforme descrito na tabela a seguir:

   |Campo  |Description  |
   |---------|---------|
   |**Escopo**   |  Indica quantas assinaturas podem usar o benefício de cobrança associado à reserva. Também controla como a reserva será aplicada a assinaturas específicas. <br/><br/> Se você selecionar **compartilhado**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure em qualquer assinatura no contexto de cobrança. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes pagos conforme o uso, o escopo compartilhado inclui todas as assinaturas individuais com tarifas pagas conforme o uso criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **assinatura única**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada. <br/><br/> Se você selecionar **um único grupo de recursos**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura. <br/><br/> Você pode alterar o escopo de reserva depois de comprar a reserva.  |
   |**Assinatura**  | A assinatura que é usada para pagar pela reserva de armazenamento do Azure. O método de pagamento na assinatura selecionada é usado para cobrar os custos. A assinatura deve ser um dos seguintes tipos: <br/><br/>  Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): para uma assinatura corporativa, os encargos são deduzidos do saldo de compromisso monetário do registro ou cobrados como excedentes. <br/><br/> Assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): para uma assinatura individual com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.    |
   | **Discos** | A SKU que você pretende criar. |
   | **Região** | A região em que a reserva está em vigor. |
   | **Frequência de cobrança** | Indica com que frequência a conta é cobrada pela reserva. As opções incluem *mensalmente* ou *antecipadamente*. |

    ![Premium-SSD-reserved-Purchase-Selection. png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Depois de selecionar os parâmetros para sua reserva, o portal do Azure exibirá o custo. O portal também mostra a porcentagem de desconto sobre a cobrança paga conforme o uso. Selecione **Avançar** para prosseguir para a folha **reservas de compra** .

1. No painel **reservas de compra** , você pode nomear sua reserva e selecionar a quantidade total de reservas que deseja fazer. O número de reservas é mapeado para o número de discos. Por exemplo, se você quisesse reservar uma centena de discos, seria possível alterar a **quantidade** para 100.
1. Examine o custo total da reserva.

    ![Premium-SSD-reserved-Selecting-SKU-total-Purchase. png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Depois de comprar uma reserva, ela é aplicada automaticamente a todos os recursos existentes de armazenamento em disco do Azure que correspondem aos termos da reserva. Se você ainda não tiver criado recursos de armazenamento em disco do Azure, a reserva será aplicada sempre que você criar um recurso que corresponda aos termos da reserva. Em ambos os casos, o termo da reserva começa imediatamente após uma compra bem-sucedida.

## <a name="exchange-or-refund-a-reservation"></a>Trocar ou reembolsar uma reserva

Com certas limitações, você pode trocar ou reembolsar uma reserva.

Para trocar ou reembolsar uma reserva, navegue até os detalhes de reserva na portal do Azure. Selecione **Exchange ou reembolso**e siga as instruções para enviar uma solicitação de suporte. Quando a solicitação tiver sido processada, a Microsoft lhe enviará um email para confirmar a conclusão da solicitação.

Para obter mais informações sobre as políticas de reservas do Azure, consulte [trocas e reembolsos de autoatendimento para reservas do Azure](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Trocar uma reserva

A troca de uma reserva permite que você receba um reembolso rateado com base na parte não usada da reserva. Em seguida, você pode aplicar o reembolso ao preço de compra de uma nova reserva de disco do Azure.
Não há limite para o número de trocas que podem ser feitas. Além disso, não há taxas associadas a uma troca. A nova reserva que você compra deve ser de valor igual ou maior do que o crédito rateado da reserva original. Uma reserva de disco do Azure pode ser trocada apenas para outra reserva de disco do Azure e não para uma reserva para qualquer outro serviço do Azure.

### <a name="refund-a-reservation"></a>Reembolsar uma reserva

Você pode cancelar uma reserva de disco do Azure a qualquer momento. Se você cancelar, receberá um reembolso rateado com base no termo restante da reserva, menos uma taxa de rescisão inicial de 12%. O reembolso máximo por ano é de $50000.
O cancelamento de uma reserva termina imediatamente a reserva e retorna os meses restantes à Microsoft. O saldo rateado restante, menos a taxa, será reembolsado em sua forma original de compra.

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade de disco do Azure que você esteja usando sob essa reserva é cobrada com a taxa paga conforme o uso. As reservas não são renovadas automaticamente.
Você receberá uma notificação por email 30 dias antes da expiração da reserva e novamente na data de validade. Para continuar aproveitando a economia de custos que uma reserva fornece, renove-a não depois da data de expiração.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contate-nos

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximos passos

- [O que são Reservas do Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Entenda como seu desconto de reserva é aplicado a Armazenamento em Disco do Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md)