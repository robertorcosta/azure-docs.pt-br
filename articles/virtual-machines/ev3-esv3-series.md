---
title: SérieEv3 e Série Esv3 - Azure Virtual Machines
description: Especificações para as VMs da série Ev3 e Esv3.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: aad4610a44c59ed95cf1ad9777329097886c5bed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164365"
---
# <a name="ev3-and-esv3-series"></a>Séries Ev3 e Esv3

A série Ev3 e Esv3 apresentam o processador Intel® Xeon® 8171M 2.1 GHz (Skylake) ou o processador Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) em uma configuração hiper-threaded, fornecendo uma melhor proposta de valor para a maioria das cargas de trabalho de propósito geral, e trazendo o Ev3 em alinhamento com o propósito geral VMs da maioria das outras nuvens.  A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites de rede e disco foram ajustados em uma base por núcleo para alinhar com a mudança para o hyperthreading. A série Ev3 é o acompanhamento até os tamanhos de VM de memória alta das famílias D/Dv2.

## <a name="ev3-series"></a>Ev3-series

As instâncias da série Ev3 são baseadas no recurso do processador Intel® Xeon® 8171M 2.1 GHz (Skylake) ou do processador Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) e da Tecnologia Intel Turbo Boost 2.0. As instâncias Ev3-series são ideais para aplicativos empresariais com uso intensivo de memória.

O armazenamento do disco de dados é faturado separadamente das máquinas virtuais. Para usar discos de armazenamento premium, use os tamanhos ESv3. Os medidores de cobrança e preço para os tamanhos Esv3 são os mesmos da Ev3-series.

O recurso da Série Ev3 VM Intel® Tecnologia Hyper-Threading.

ACU: 160 - 190

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | NICs máximas / largura de banda da rede |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1,2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> Tamanhos de núcleo restritos disponíveis.

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

## <a name="esv3-series"></a>Série Esv3

As instâncias da série Esv3 são baseadas no recurso do processador Intel® Xeon® 8171M 2.1 GHz (Skylake) ou do processador Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), da Intel Turbo Boost Technology 2.0 e usam armazenamento premium. As instâncias da série Esv3 são ideais para aplicações corporativas que consomem muito memória.

O recurso da Série Esv3 VM Intel® Tecnologia Hyper-Threading.

ACU: 160-190

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de armazenamento em cache e armazenamento temporário: IOPS/MBps (tamanho de cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| <sup>Standard_E8s_v3 1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup> Tamanhos de núcleo restritos disponíveis.

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

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
