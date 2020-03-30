---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334705"
---
| Recurso | Limite |
| --- | --- |
| VMs por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> por região. |
| Total de núcleos da VM por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| Núcleos totais do Azure Spot VM por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| VM por série, como Dv2 e F, núcleos por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| [Disponibilidade define](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por assinatura |2.000 por região. |
| Máquinas virtuais por conjunto de disponibilidade | 200 |
| Certificados por assinatura |Ilimitado<sup>2</sup> |

<sup>1</sup> Os limites padrão variam de acordo com o tipo de categoria de oferta, como Teste Livre e Pay-As-You-Go, e por séries, como Dv2, F e G. Por exemplo, o padrão para assinaturas do Enterprise Agreement é 350.

<sup>2</sup> Com o Azure Resource Manager, os certificados são armazenados no Cofre de Chaves do Azure. O número de certificados é ilimitado para uma assinatura. Há um limite de 1 MB de certificados por implantação, que consiste em uma única VM ou um conjunto de disponibilidade.

> [!NOTE]
> Os núcleos de máquinas virtuais têm um limite total regional. Eles também têm um limite para séries regionais por tamanho, como Dv2 e F. Esses limites são impostos separadamente. Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Esta assinatura pode implantar 30 VMs A1, ou 30 VMs D1, ou uma combinação das duas para não exceder um total de 30 núcleos. Um exemplo de combinação são 10 VMs A1 e 20 VMs D1.  
> <!-- -->
> 
