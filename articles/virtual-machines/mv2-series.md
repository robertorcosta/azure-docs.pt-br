---
title: Série Mv2-máquinas virtuais do Azure
description: Especificações para as VMs da série Mv2.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: caa80443a189874bfd699dba412fb749c8711556
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493391"
---
# <a name="mv2-series"></a>Série Mv2

A Mv2-Series apresenta alta taxa de transferência, plataforma de baixa latência em execução em um processador Hyper-Threading Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) com uma frequência base básica de 2,5 GHz e uma frequência máxima de Turbo de 3,8 GHz. Todos os tamanhos de máquina virtual da série Mv2 podem usar discos persistentes Standard e Premium. As instâncias da série Mv2 são tamanhos de VM com otimização de memória que fornecem desempenho computacional inigualável para dar suporte a grandes bancos de dados na memória e cargas de trabalho, com uma alta taxa de memória para CPU, ideal para servidores de banco de dados relacionais, caches grandes e na memória Analytics.

O recurso da VM da série Mv2 Intel® tecnologia Hyper-Threading

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Acelerador de Gravação: [com suporte](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1, 2</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1, 2</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> as VMs da série Mv2 são apenas de geração 2. Se você estiver usando o Linux, consulte [suporte para VMs de geração 2 no Azure](/linux/generation-2.md) para obter instruções sobre como localizar e selecionar uma imagem.

<sup>2</sup> para os tamanhos de M416ms_v2 e M416s_v2, observe que há suporte inicial apenas para a imagem a seguir: "GEN2: SuSE Linux Enterprise Server (SLES) 12 SP4 para aplicativos SAP".

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