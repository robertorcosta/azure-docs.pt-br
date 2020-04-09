---
title: Série Mv2 - Azure Virtual Machines
description: Especificações para as VMs da série MV2.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 764dc93608ae3b8882b7048a722c6d3415cbc644
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885779"
---
# <a name="mv2-series"></a>Série Mv2

A série Mv2 possui alta taxa de entrada, plataforma de baixa latência rodando em um processador Intel® Xeon hiper-roscado® Platinum 8180M 2.5GHz (Skylake) com uma frequência base de 2,5 GHz e uma frequência turbo máxima de 3,8 GHz. Todos os tamanhos de máquinas virtuais da série Mv2 podem usar discos padrão e premium persistentes. As instâncias da série Mv2 são tamanhos de VM otimizados para memória que fornecem desempenho computacional incomparável para suportar grandes bancos de dados e cargas de trabalho na memória, com uma alta relação memória-CPU que é ideal para servidores de banco de dados relacionais, grandes caches e análises na memória.

Recurso da Série MV2 VM Intel® Tecnologia hyper-threading

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

Write Accelerator: [Suportado](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| <sup>Standard_M416ms_v2 1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> VMs da série Mv2 são apenas a geração 2. Se você estiver usando o Linux, consulte [suporte para VMs de geração 2 no Azure](./linux/generation-2.md) para obter instruções sobre como encontrar e selecionar uma imagem. Se você estiver usando o Windows, consulte [suporte para VMs de geração 2 no Azure](./windows/generation-2.md) para obter instruções sobre como encontrar e selecionar uma imagem. As versões mínimas do sistema operacional necessárias para todos os quatro tipos de VM da série Mv2 diferentes, como:

- Windows Server 2019 ou posterior
- SUSE Linux Enterprise Server 12 SP4 e posteriormente ou SUSE Linux Enterprise Server 15 SP1 e posterior
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 ou posterior 
- Oracle Enterprise Linux 7.7 ou posterior



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Otimizado para memória](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
