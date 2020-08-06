---
title: Série Mv2-máquinas virtuais do Azure
description: Especificações para as VMs da série Mv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: f86400f1fb45217133bbd715788986994f5f77cc
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836251"
---
# <a name="mv2-series"></a>Série Mv2

A Mv2-Series apresenta alta taxa de transferência, plataforma de baixa latência em execução em um processador Hyper-Threading Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) com uma frequência base básica de 2,5 GHz e uma frequência máxima de Turbo de 3,8 GHz. Todos os tamanhos de máquina virtual da série Mv2 podem usar discos persistentes Standard e Premium. As instâncias da série Mv2 são tamanhos de VM com otimização de memória que fornecem desempenho computacional inigualável para dar suporte a grandes bancos de dados na memória e cargas de trabalho, com uma alta taxa de memória para CPU, ideal para servidores de banco de dados relacionais, caches grandes e análise na memória.

O recurso da VM da série Mv2 Intel® tecnologia Hyper-Threading

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Migração ao Vivo: Sem suporte

Atualizações de preservação de memória: Sem suporte

Acelerador de Gravação: [com suporte](./windows/how-to-enable-write-accelerator.md)

|Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs | Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |

<sup>1</sup> as VMs da série Mv2 são somente geração 2 e dão suporte a um subconjunto de imagens com suporte de geração 2. Veja abaixo a lista completa de imagens com suporte para a série Mv2. Se você estiver usando o Linux, consulte [suporte para VMs de geração 2 no Azure](./linux/generation-2.md) para obter instruções sobre como localizar e selecionar uma imagem. Se você estiver usando o Windows, consulte [suporte para VMs de geração 2 no Azure](./windows/generation-2.md) para obter instruções sobre como localizar e selecionar uma imagem. 

- Windows Server 2019 ou posterior
- SUSE Linux Enterprise Server 12 SP4 e posterior ou SUSE Linux Enterprise Server 15 SP1 e posterior
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 ou posterior 
- Oracle Enterprise Linux 7,7 ou posterior



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos: [tipos de disco](./linux/disks-types.md#ultra-disk)


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.