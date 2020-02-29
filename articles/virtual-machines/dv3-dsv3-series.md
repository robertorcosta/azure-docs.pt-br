---
title: Dv3 e série Dsv3-máquinas virtuais do Azure
description: Especificações para as VMs da série Dv3 e Dsv3.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164399"
---
# <a name="dv3-and-dsv3-series"></a>Séries Dv3 e DSv3

A série Dv3 é executada no Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) em uma configuração de Hyper-thread, fornecendo uma melhor proposta de valor para o mais geral cargas de trabalho de finalidade. A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites de rede e disco em uma base por núcleo foram ajustados para alinhar com a mudança para o hyperthreading. A série Dv3 não tem mais os tamanhos de VM de memória alta da série D/Dv2, elas foram movidas para o Ev3 de memória otimizada e para a [série Esv3](ev3-esv3-series.md).

Exemplos de casos de uso da série D incluem aplicativos de nível empresarial, bancos de dados relacionais, cache na memória e análise.

## <a name="dv3-series"></a>Dv3-series

Os tamanhos da série Dv3 são executados no Intel® Xeon® 8171M 2.1 GHz (Skylake), no Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) com a tecnologia Intel Turbo Boost 2,0. Os tamanhos da série Dv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

O armazenamento do disco de dados é faturado separadamente das máquinas virtuais. Para usar discos de armazenamento premium, use os tamanhos Dsv3. Os medidores de cobrança e preço para os tamanhos Dsv3 são os mesmos que os da Dv3-series.

O recurso de VMs da série Dv3 Intel® tecnologia Hyper-Threading.

ACU: 160-190

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

Migração ao Vivo: com suporte

Atualizações de preservação de memória: com suporte

| Size | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Largura de banda máxima de NICs/rede |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3-series

Os tamanhos da série Dsv3 são executados no Intel® Xeon® 8171M 2.1 GHz (Skylake), no Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) com a tecnologia Intel Turbo Boost 2,0 e usam o armazenamento Premium. Os tamanhos da série Dsv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

O recurso de VMs da série Dsv3 Intel® tecnologia Hyper-Threading.

ACU: 160-190

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Migração ao Vivo: com suporte

Atualizações de preservação de memória: com suporte

| Size | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
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
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
