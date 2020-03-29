---
title: Série HBv2 - Azure Virtual Machines
description: Especificações para as VMs da série HBv2.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164790"
---
# <a name="hbv2-series"></a>Série HBv2

As VMs da série HBv2 são otimizadas para aplicações impulsionadas pela largura de banda da memória, como dinâmica de fluidos, análise de elementos finitos e simulação de reservatório. As VMs HBv2 possuem 120 núcleos de processador AMD EPYC 7742, 4 GB de RAM por núcleo de CPU e nenhuma multithreading simultânea. Cada VM HBv2 fornece até 340 GB/seg de largura de banda de memória e até 4 teraFLOPS de computação FP64.

Armazenamento Premium: com suporte

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Freqüência da CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho RDMA (Gb/s) | Suporte ao MPI | Armazenamento temporário (GB) | Discos de dados máximos | Max Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Todos | 480 + 960 | 8 | 1 |


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