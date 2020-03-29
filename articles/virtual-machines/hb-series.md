---
title: HB-series - Azure Virtual Machines
description: Especificações para as VMs da série HB.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164824"
---
# <a name="hb-series"></a>Série HB

As VMs da série HB são otimizadas para aplicações impulsionadas pela largura de banda da memória, como dinâmica de fluidos, análise explícita de elementos finitos e modelagem meteorológica. Hb VMs possuem 60 núcleos de processador AMD EPYC 7551, 4 GB de RAM por núcleo de CPU e sem multithreading simultâneo. Um HB VM fornece até 260 GB/seg de largura de banda de memória.

ACU: 199-216

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Freqüência da CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho RDMA (Gb/s) | Suporte ao MPI | Armazenamento temporário (GB) | Discos de dados máximos | Max Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2,0 | 2.55 | 2.55 | 100 | Todos | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.