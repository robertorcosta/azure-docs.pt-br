---
title: Série HB
description: Especificações para as VMs da série HB.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: b0ffd9af8df830c1bdacbfe3809d4086346679b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678555"
---
# <a name="hb-series"></a>Série HB

As VMs da série HB são otimizadas para aplicativos orientados por largura de banda de memória, como dinâmica de fluidos, análise de elemento finito explícito e modelagem de clima. HB VMs Feature 60 AMD EPYC 7551 núcleos de processador, 4 GB de RAM por núcleo de CPU e nenhum multithread simultâneo. Uma VM HB fornece até 260 GB/s de largura de banda de memória.

ACU: 199-216

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte

Migração ao Vivo: Sem suporte

Atualizações de preservação de memória: Sem suporte

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GB) | Discos de dados máximos | Máximo de NICs Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2,0 | 2.55 | 2.55 | 100 | Tudo | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.