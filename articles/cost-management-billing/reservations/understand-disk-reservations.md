---
title: Entenda como um desconto de reserva é aplicado ao Armazenamento em Disco do Azure
description: Saiba como o desconto dos discos reservados do Azure é aplicado aos seus discos gerenciados do SSD Premium.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902132"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Entenda como o desconto de reserva é aplicado ao Armazenamento em Disco do Azure

Depois de comprar a capacidade reservada de disco do Azure, o desconto de reserva será aplicado automaticamente aos recursos do disco que correspondem aos termos da reserva. O desconto de reserva aplica-se somente à capacidade do disco, os instantâneos de disco são cobrados com tarifas pagas conforme o uso.

Para obter mais informações sobre a reserva de Disco do Azure, confira [Economizar custos com a Reserva de Discos do Azure](../../virtual-machines/linux/disks-reserved-capacity.md).
Para obter informações sobre preços de reserva de Disco do Azure, confira [Preços de Disco do Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

O desconto de reserva de discos do Azure é um desconto que deve ser usado até uma determinada data e é aplicado aos recursos de disco gerenciado por hora. Se você não tiver recursos de disco gerenciado que atendam aos termos da reserva de uma determinada hora, perderá uma quantidade de reserva para essa hora. As horas não usadas não serão postergadas.

Quando você exclui um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão perdidas.

## <a name="discount-examples"></a>Exemplos de desconto

Os seguinte exemplos mostram como o desconto de reserva de Disco do Azure se aplica, dependendo das implantações:

Suponha que você tenha comprado 100 discos P30 (aproximadamente 1 TiB por disco) de capacidade reservada na região Oeste dos EUA 2 pelo período de um ano. Suponha que o custo desta reserva de exemplo seja de US$ 140.100. Você pode optar por fazer o pagamento antecipado do valor total ou pagar parcelas mensais fixas de US$ 11.675 nos próximos 12 meses.
Para esses exemplos, vamos supor que você tenha se inscrito em um plano de pagamento mensal de reservas. Os cenários a seguir descrevem o que acontece se você subutiliza, usa excessivamente ou divide em níveis sua capacidade reservada.

### <a name="underusing-your-capacity"></a>Subutilização da sua capacidade

Suponha que, em uma determinada hora dentro do período de reserva, você tenha implantado somente 99 SSDs P30 Premium de sua reserva de discos de 100 P30. O disco P30 restante não será aplicado a essa hora e não será transferido.

### <a name="overusing-your-capacity"></a>Superutilização da sua capacidade

Suponha que, em uma determinada hora dentro do período de reserva, você esteja usando 101 SSDs P30 Premium. O desconto da reserva somente se aplica a 100 discos P30 e o disco P30 restante será cobrado com tarifas pagas conforme o uso para aquela hora. Se, na próxima hora, seu uso diminuir para 100 discos P30, todo o uso será coberto pela reserva.

### <a name="tiering-your-capacity"></a>Divisão em camadas de sua capacidade

Suponha que, em uma determinada hora em seu período de reserva, você queira usar o total de 200 SSDs P30 Premium. Nos primeiros 30 minutos, você só usa 100. Durante esse período, seu uso é totalmente coberto, pois você fez uma reserva de 100 discos P30. Se, em seguida, você descontinuar o uso dos primeiros 100 (ou seja, está usando zero) e começar a usar os outros 100 pelos 30 minutos restantes, esse uso também estará coberto por sua reserva.

![Representação dos exemplos de capacidade em subutilização, uso excessivo ou dividida em níveis](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Reduzir custos com a Reserva de Discos do Azure (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Reduzir custos com a Reserva de Discos do Azure (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [O que são Reservas do Azure?](save-compute-costs-reservations.md)