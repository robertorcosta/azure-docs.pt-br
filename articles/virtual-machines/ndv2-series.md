---
title: Série NDv2 - Azure Virtual Machines
description: Especificações para as VMs da série NDv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247275"
---
# <a name="updated-ndv2-series"></a>Série NDv2 atualizada

A máquina virtual da série NDv2 é uma nova adição à família de GPU projetada para as necessidades das mais exigentes cargas de trabalho aceleradas de GPU, aprendizado de máquina, simulação e HPC.

O NDv2 é alimentado por 8 GPUs conectadas ao NVIDIA Tesla V100 NVLINK, cada uma com 32 GB de memória GPU. Cada VM NDv2 também tem 40 núcleos Intel Xeon Platinum 8168 (Skylake) não-HyperThreaded e 672 GiB de memória do sistema.

As instâncias NDv2 fornecem excelente desempenho para cargas de trabalho de HPC e IA que utilizam núcleos de computação otimizados para GPU CUDA e as muitas ferramentas de IA, ML e analytics que suportam a aceleração de GPU 'fora da caixa', como TensorFlow, Pytorch, Caffe, RAPIDS e outros Estruturas.

Criticamente, o NDv2 é construído para cargas de trabalho de escala computação intensa (aproveitando 8 GPUs por VM) e scale-out (aproveitando várias VMs trabalhando juntas). A série NDv2 agora suporta rede backend Edr 100-Gigabit InfiniBand, semelhante à disponível na série HB do HPC VM, para permitir clustering de alto desempenho para cenários paralelos, incluindo treinamento distribuído para IA e ML. Essa rede backend suporta todos os principais protocolos InfiniBand, incluindo os empregados pelas bibliotecas NCCL2 da NVIDIA, permitindo o agrupamento perfeito de GPUs.

> Ao ativar o [InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) na VM ND40rs_v2, use o driver 4.7-1.0.0.1 Mellanox OFED.
>
> Devido ao aumento da memória da GPU, o novo ND40rs_v2 VM requer o uso de [VMs de geração 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) e imagens de mercado. 
>
> Nota: O ND40s_v2 com 16 GB de memória por GPU não está mais disponível para visualização e foi substituído pela ND40rs_v2 atualizada.

<br>

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

InfiniBand: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória gpu: GiB | Discos de dados máximos | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Largura de banda de rede máxima | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série Azure N, os drivers de GPU NVIDIA devem ser instalados.

A [Extensão de Driver de GPU NVIDIA](./extensions/hpccompute-gpu-linux.md) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](./extensions/overview.md).

Se você optar por instalar drivers DE GPU NVIDIA manualmente, consulte [a configuração do driver de GPU da série N para Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
