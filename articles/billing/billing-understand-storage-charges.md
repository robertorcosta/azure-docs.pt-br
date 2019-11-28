---
title: Entenda como o desconto de reserva é aplicado ao Armazenamento do Azure | Microsoft Docs
description: Saiba mais sobre como o desconto de capacidade reservada do Armazenamento do Azure é aplicado ao blob de blocos e recursos do Azure Data Lake Storage Gen2.
author: tamram
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.openlocfilehash: 4dd224036989b4ce78ee382b539e99c12015128e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225745"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Entenda como o desconto de reserva é aplicado ao Armazenamento do Azure

Depois de adquirir a capacidade reservada do Armazenamento do Azure, o desconto de reserva é aplicado automaticamente ao blob de blocos e aos recursos do Azure Data Lake Storage Gen2 que correspondem aos termos da reserva. O desconto de reserva se aplicado às capacidades de armazenamento apenas. A largura de banda e a taxa de solicitação são cobradas com taxas pré-pagas.

Para saber mais sobre a capacidade reservada do Armazenamento do Azure, confira [Otimizar os custos do armazenamento de blobs com capacidade reservada](../storage/blobs/storage-blob-reserved-capacity.md).

Para saber mais sobre os preços de reserva do Armazenamento do Azure, confira [Preços do blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/) e [Preços do Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="how-the-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

O desconto de capacidade reservada do Armazenamento do Azure é aplicado ao blob de blocos e recursos do Azure Data Lake Storage Gen2 a cada hora.

O desconto de capacidade reservada do Armazenamento do Azure é um desconto do tipo "pegar ou largar". Se você não tiver blobs de blocos ou recursos do Azure Data Lake Storage Gen2 que atendam aos termos da reserva de uma determinada hora, você perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você exclui um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão perdidas.

## <a name="discount-examples"></a>Exemplos de desconto

Os exemplos a seguir mostram como o desconto de capacidade reservada do Armazenamento do Azure se aplica, dependendo das implantações.

Suponha que você tenha comprado 100 TB de capacidade reservada na região Oeste dos EUA 2 pelo período de um ano. Sua reserva é para o LRS (Armazenamento com Redundância Local) na camada de acesso frequente.

Suponha que o custo desta reserva de exemplo seja de US$ 18.540. Você pode optar por fazer o pagamento antecipado do valor total ou pagar parcelas mensais fixas de US$ 1.545 nos próximos 12 meses.

Para esses exemplos, vamos supor que você tenha se inscrito em um plano de pagamento mensal de reservas. Os cenários a seguir descrevem o que acontece se você subutilizar ou utilizar excessivamente sua capacidade reservada.

### <a name="underusing-your-capacity"></a>Subutilização da sua capacidade

Suponha que, em uma determinada hora dentro do período de reserva, você tenha usado apenas 80 TB de sua capacidade reservada de 100 TB. Os 20 TB restantes não são aplicados a essa hora e não são transferidos.

### <a name="overusing-your-capacity"></a>Superutilização da sua capacidade

Suponha que, em uma determinada hora dentro do período de reserva, você tenha usado 101 TB de sua capacidade reservada. O desconto da reserva se aplica a 100 TB dos dados e os 1 TB restantes são cobrados com tarifas pré-pagas para aquela hora. Se, na próxima hora, seu uso mudar para 100 TB, todo o uso será coberto pela reserva.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Otimizar custos para o armazenamento de blobs com capacidade reservada](../storage/blobs/storage-blob-reserved-capacity.md)
- [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
