---
title: Sériedv3 e Dsv3 - Azure Virtual Machines
description: Especificações para as VMs da série Dv3 e Dsv3.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164399"
---
# <a name="dv3-and-dsv3-series"></a>Séries Dv3 e DSv3

A série Dv3 é executada no Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) em uma configuração hiper-threaded, fornecendo uma melhor proposta de valor para a maioria dos processadores gerais cargas de trabalho de propósito. A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites de rede e disco em uma base por núcleo foram ajustados para alinhar com a mudança para o hyperthreading. A série Dv3 não tem mais os tamanhos de VM de alta memória da série D/Dv2, que foram movidos para a memória otimizada [sérieEv3 e Esv3](ev3-esv3-series.md).

Os casos de uso da série D incluem aplicativos de nível corporativo, bancos de dados relacionais, cache na memória e análises.

## <a name="dv3-series"></a>Dv3-series

Os tamanhos da série Dv3 são executados nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com tecnologia Intel Turbo Boost 2.0. Os tamanhos da série Dv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

O armazenamento do disco de dados é faturado separadamente das máquinas virtuais. Para usar discos de armazenamento premium, use os tamanhos Dsv3. Os medidores de cobrança e preço para os tamanhos Dsv3 são os mesmos que os da Dv3-series.

As VMs da série Dv3 possuem tecnologia Intel® Hyper-Threading.

ACU: 160-190

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Throughput de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Largura de banda max NICs/Rede |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3-series

Os tamanhos da série DSV3 são executados nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com tecnologia Intel Turbo Boost 2.0 e armazenamento premium. Os tamanhos da série Dsv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

As VMs da série DSV3 possuem tecnologia Intel® Hyper-Threading.

ACU: 160-190

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de armazenamento em cache e armazenamento temporário: IOPS/MBps (tamanho de cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

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
