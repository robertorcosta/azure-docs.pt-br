---
title: Série HBv2-máquinas virtuais do Azure
description: Especificações para as VMs da série HBv2.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: 730065333fde7431b7018ef0a6e614e12727d7ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84673846"
---
# <a name="hbv2-series"></a>Série HBv2

As VMs da série HBv2 são otimizadas para aplicativos orientados por largura de banda de memória, como dinâmica de fluidos, análise de elemento finito e simulação de reservatório. HBv2 VMs Feature 120 AMD EPYC 7742 núcleos de processador, 4 GB de RAM por núcleo de CPU e nenhum multithread simultâneo. Cada VM HBv2 fornece até 340 GB/s de largura de banda de memória e até 4 teraFLOPS de computação FP64.

Armazenamento Premium: com suporte

Migração ao Vivo: Sem suporte

Atualizações de preservação de memória: Sem suporte

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GB) | Discos de dados máximos | Máximo de NICs Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Tudo | 480 + 960 | 8 | 1 |


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