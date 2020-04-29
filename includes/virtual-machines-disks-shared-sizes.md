---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008311"
---
Por enquanto, somente os ultra discos e o SSDs Premium podem habilitar discos compartilhados. Tamanhos de disco diferentes podem ter um `maxShares` limite diferente, que você não pode exceder `maxShares` ao definir o valor. Para o SSDs Premium, os tamanhos de disco que dão suporte ao compartilhamento de discos são P15 e superior.

Para cada disco, você pode definir um `maxShares` valor que representa o número máximo de nós que podem compartilhar o disco simultaneamente. Por exemplo, se você planeja configurar um cluster de failover de 2 nós, defina `maxShares=2`. O valor máximo é um limite superior. Os nós podem ingressar ou sair do cluster (montar ou desmontar o disco), desde que o número de nós seja menor do `maxShares` que o valor especificado.

> [!NOTE]
> O `maxShares` valor só pode ser definido ou editado quando o disco é desanexado de todos os nós.

### <a name="premium-ssd-ranges"></a>Intervalos de SSD Premium

A tabela a seguir ilustra os valores máximos `maxShares` permitidos para os tamanhos de disco Premium:

|Tamanhos do disco  |limite de maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Os limites de IOPS e largura de banda de um disco não são `maxShares` afetados pelo valor. Por exemplo, o IOPS máximo de um disco P15 é 1100 se maxShares = 1 ou maxShares > 1.

### <a name="ultra-disk-ranges"></a>Intervalos de ultra Disk

O valor `maxShares` mínimo é 1, enquanto o valor `maxShares` máximo é 5. Não há restrições de tamanho em ultra discos, qualquer tamanho de disco pode usar qualquer valor para `maxShares`, até e incluindo o valor máximo.