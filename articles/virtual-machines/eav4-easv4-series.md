---
title: Série Eav4 e Série Easv4 - Azure Virtual Machines
description: Especificações para as VMs da série Eav4 e Easv4.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: e05a2b97a25ce4cab7d619f4b62d405765cb08db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164382"
---
# <a name="eav4-and-easv4-series"></a>Séries Eav4 e Easv4

As séries Eav4 e Easv4 utilizam o processador AMD 2.35Ghz<sup>TM</sup> 7452 em uma configuração multi-threaded com cache de até 256MB L3, aumentando as opções para executar a maioria das cargas de trabalho otimizadas de memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que a série Ev3 & Série Esv3.

## <a name="eav4-series"></a>Série Eav4

ACU: 230 - 260

Armazenamento premium: não suportado

Cache de armazenamento premium: não suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

Os tamanhos da série Eav4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2,35Ghz que pode alcançar uma frequência máxima aumentada de 3,35GHz e usar SSD premium. Os tamanhos da série Eav4 são ideais para aplicações corporativas que consomem muito tempo. O armazenamento do disco de dados é faturado separadamente das máquinas virtuais. Para usar SSD premium, use os tamanhos da série Easv4. Os medidores de preços e faturamento para tamanhos Easv4 são os mesmos da série Eav3.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Máximo NICs / Largura de banda de rede esperada (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| Padrão\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| Padrão\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| Padrão\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Padrão\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Padrão\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| Padrão\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| Padrão\_E48a\_v4<sup>**</sup> |48|384|1200|32| | |
| Padrão\_E64a\_v4<sup>**</sup> |64|512|1600|32| | |
| Padrão\_E96a\_v4<sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>Esses tamanhos estão em Visualização. Se você estiver interessado em experimentar esses tamanhos maiores, inscreva-se em [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="easv4-series"></a>Série Easv4

ACU: 230 - 260

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

Os tamanhos da série Easv4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2,35Ghz que pode alcançar uma frequência máxima aumentada de 3,35GHz e usar SSD premium. Os tamanhos da série Easv4 são ideais para aplicações corporativas que consomem muito tempo.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo NICs / Largura de banda de rede esperada (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_E48as_v4<sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4<sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4<sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>Esses tamanhos estão em Visualização. Se você estiver interessado em experimentar esses tamanhos maiores, inscreva-se em [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

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
