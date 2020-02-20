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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471665"
---
Por enquanto, somente o SSDs Premium pode habilitar discos compartilhados. Os tamanhos de disco que dão suporte a esse recurso são P15 e superior. Tamanhos de disco diferentes podem ter um limite de `maxShares` diferente, que você não pode exceder ao definir o valor de `maxShares`.

Para cada disco, você pode definir um valor `maxShares` que representa o número máximo de nós que podem compartilhar o disco simultaneamente. Por exemplo, se você planeja configurar um cluster de failover de 2 nós, defina `maxShares=2`. O valor máximo é um limite superior. Os nós podem ingressar ou sair do cluster (montar ou desmontar o disco), desde que o número de nós seja menor do que o valor de `maxShares` especificado.

> [!NOTE]
> O valor `maxShares` só pode ser definido ou editado quando o disco é desanexado de todos os nós.

A tabela a seguir ilustra os valores máximos permitidos para `maxShares` por tamanho de disco:

|Tamanhos do disco  |limite de maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Os limites de IOPS e largura de banda de um disco não são afetados pelo valor `maxShares`. Por exemplo, o IOPS máximo de um disco P15 é de 1100 se maxShares = 1 ou maxShares > 1.