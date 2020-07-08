---
title: Série Ev3 e série Esv3
description: Especificações para as VMs da série Ev3 e Esv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9fcb714e68548b95ed6d32bb16fb08c43ff0e5fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707626"
---
# <a name="ev3-and-esv3-series"></a>Séries Ev3 e Esv3

A Ev3 e a série Esv3 são executadas no Intel® Xeon® Platinum 8272CL (Cascade Lake), ou o Intel® Xeon® 8171M 2,1 GHz (Skylake) ou o processador Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) em uma configuração de hiperthread, fornecendo uma proposta de valor melhor para as cargas de trabalho de uso geral e trazendo a Ev3 para o alinhamento com as VMs de uso geral da maioria das outras nuvens.  A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites de rede e disco foram ajustados em uma base por núcleo para alinhar com a mudança para o hyperthreading. A série Ev3 é o acompanhamento até os tamanhos de VM de memória alta das famílias D/Dv2.

## <a name="ev3-series"></a>Ev3-series

As instâncias da série Ev3 são executadas no Intel® Xeon® Platinum 8272CL (Cascadey Lake), no Intel® Xeon® 8171M 2,1 GHz (Skylake) ou nos processadores Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) e no recurso Intel Turbo Boost Technology 2,0. As instâncias Ev3-series são ideais para aplicativos empresariais com uso intensivo de memória.

O armazenamento do disco de dados é faturado separadamente das máquinas virtuais. Para usar discos de armazenamento premium, use os tamanhos ESv3. Os medidores de cobrança e preço para os tamanhos Esv3 são os mesmos da Ev3-series.

O recurso da VM da série Ev3 Intel® tecnologia Hyper-Threading.

ACU: 160 - 190

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

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
| Standard_E64i_v3 <sup>1, 2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> tamanhos de núcleos restritos disponíveis.

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

## <a name="esv3-series"></a>Série Esv3

As instâncias da série Esv3 são executadas no Intel® Xeon® Platinum 8272CL (Cascadey Lake), no Intel® Xeon® 8171M 2,1 GHz (Skylake) ou no processador Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), o recurso Intel Turbo Boost Technology 2,0 e usa o armazenamento Premium. As instâncias da série Esv3 são ideais para aplicativos empresariais com uso intensivo de memória.

O recurso da VM da série Esv3 Intel® tecnologia Hyper-Threading.

ACU: 160-190

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup> tamanhos de núcleos restritos disponíveis.

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

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
