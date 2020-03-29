---
title: Sériedv2 e Dsv2 - Azure Virtual Machines
description: Especificações para as VMs da série Dv2 e DSV2.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164416"
---
# <a name="dv2-and-dsv2-series"></a>Séries Dv2 e DSv2

As séries Dv2 e Dsv2, uma continuação da série D original, apresentam uma CPU mais poderosa e uma configuração ótima de CPU para memória, tornando-as adequadas para a maioria das cargas de trabalho de produção. A série Dv2 é cerca de 35% mais rápida que a série D. A série DV2 é executada nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com a Tecnologia Intel Turbo Boost 2.0. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

## <a name="dv2-series"></a>Série Dv2

Os tamanhos da série Dv2 são executados no Intel® Xeon® 8171M 2.1GHz (Skylake) ou no Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com tecnologia Intel Turbo Boost 2.0.

ACU: 210-250

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Throughput de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados máximos | Taxa de transferência: IOPS | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4x500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8 x 500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16 x 500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32 x 500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>Série DSv2

Os tamanhos da série DSv2 são executados no Intel® Xeon® 8171M 2.1GHz (Skylake) ou nos processadores Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) ou no Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com tecnologia Intel Turbo Boost 2.0 e usam armazenamento premium.

ACU: 210-250

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de armazenamento em cache e armazenamento temporário: IOPS/MBps (tamanho de cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3,5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

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
