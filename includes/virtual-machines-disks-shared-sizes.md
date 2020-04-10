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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008311"
---
Por enquanto, apenas discos ultra e SSDs premium podem habilitar discos compartilhados. Diferentes tamanhos de `maxShares` disco podem ter um limite `maxShares` diferente, que você não pode exceder ao definir o valor. Para SSDs premium, os tamanhos de disco que suportam o compartilhamento de seus discos são P15 e maiores.

Para cada disco, você `maxShares` pode definir um valor que represente o número máximo de nomes que podem compartilhar simultaneamente o disco. Por exemplo, se você planeja configurar um cluster failover de `maxShares=2`2 nades, você definirá . O valor máximo é um limite superior. Os números podem se juntar ou deixar o cluster (montar ou desmontar o disco) `maxShares` desde que o número de nódulos seja menor do que o valor especificado.

> [!NOTE]
> O `maxShares` valor só pode ser definido ou editado quando o disco é separado de todos os nós.

### <a name="premium-ssd-ranges"></a>Faixas Premium SSD

A tabela a seguir ilustra `maxShares` os valores máximos permitidos para tamanhos de disco premium:

|Tamanhos do disco  |limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Os limites de IOPS e largura de `maxShares` banda para um disco não são afetados pelo valor. Por exemplo, o IOPS máximo de um disco P15 é de 1100, seja maxShares = 1 ou maxShares > 1.

### <a name="ultra-disk-ranges"></a>Faixas de disco ultra

O `maxShares` valor mínimo é 1, enquanto o valor máximo `maxShares` é 5. Não há restrições de tamanho em discos ultra, qualquer `maxShares`disco ultra de tamanho pode usar qualquer valor para , até e incluindo o valor máximo.