---
title: Série HB
description: Especificações para as VMs da série HB.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 14d5e5af6f485346b0e1f070e84843a9bf085126
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595335"
---
# <a name="hb-series"></a>Série HB

As VMs da série HB são otimizadas para aplicativos orientados por largura de banda de memória, como dinâmica de fluidos, análise de elemento finito explícito e modelagem de clima. HB VMs Feature 60 AMD EPYC 7551 núcleos de processador, 4 GB de RAM por núcleo de CPU e nenhum multithread simultâneo. Uma VM HB fornece até 260 GB/s de largura de banda de memória.

As VMs da série HB apresentam 100 GB/s Mellanox EDR InfiniBand. Essas VMs são conectadas em uma árvore Fat sem bloqueio para desempenho de RDMA otimizado e consistente. Essas VMs dão suporte ao roteamento adaptável e ao transporte conectado dinâmico (DCT, em outros transportes Standard RC e UD). Esses recursos aprimoram o desempenho, a escalabilidade e a consistência do aplicativo e o uso deles é altamente recomendável.

ACU: 199-216

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte

Migração ao Vivo: Sem suporte

Atualizações de preservação de memória: Sem suporte

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GB) | Discos de dados máximos | Máximo de NICs Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2,0 | 2.55 | 2.55 | 100 | Todos | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como [configurar suas VMs](./workloads/hpc/configure.md), [habilitar a InfiniBand](./workloads/hpc/enable-infiniband.md), configurar a [MPI](./workloads/hpc/setup-mpi.md)e otimizar os aplicativos HPC para o Azure em cargas de [trabalho do HPC](./workloads/hpc/overview.md).
- Leia os comunicados mais recentes e alguns exemplos e resultados da HPC nos [Blogs da Tech Community da Computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
