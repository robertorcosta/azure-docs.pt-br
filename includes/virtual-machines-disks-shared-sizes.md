---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471665"
---
Por enquanto, apenas SSDs premium podem habilitar discos compartilhados. Os tamanhos de disco que suportam esse recurso são P15 e maiores. Diferentes tamanhos de `maxShares` disco podem ter um limite `maxShares` diferente, que você não pode exceder ao definir o valor.

Para cada disco, você `maxShares` pode definir um valor que represente o número máximo de nomes que podem compartilhar simultaneamente o disco. Por exemplo, se você planeja configurar um cluster failover de `maxShares=2`2 nades, você definirá . O valor máximo é um limite superior. Os números podem se juntar ou deixar o cluster (montar ou desmontar o disco) `maxShares` desde que o número de nódulos seja menor do que o valor especificado.

> [!NOTE]
> O `maxShares` valor só pode ser definido ou editado quando o disco é separado de todos os nós.

A tabela a seguir ilustra `maxShares` os valores máximos permitidos para o tamanho do disco:

|Tamanhos do disco  |limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Os limites de IOPS e largura de `maxShares` banda para um disco não são afetados pelo valor. Por exemplo, o IOPS máximo de um disco P15 é de 1100, seja maxShares = 1 ou maxShares > 1.