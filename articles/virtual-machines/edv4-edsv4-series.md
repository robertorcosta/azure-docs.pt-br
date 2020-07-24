---
title: Séries Edv4 e Edsv4
description: Especificações para as VMs das séries Ev4, Edv4, Esv4 e Edsv4.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 3b69bb480112993c42a22c71227b64943f33585e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080362"
---
# <a name="edv4-and-edsv4-series"></a>Séries Edv4 e Edsv4

As séries Edv4 e Edsv4 são executadas nos processadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) em uma configuração hyper-threaded e são ideais para vários aplicativos empresariais com uso intensivo de memória e contam com até 504 GiB de RAM, com Tecnologia [Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) Turbo Boost 2.0, Tecnologia [Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) Hyper-Threading e [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Eles também dão suporte ao [ &reg; aumento de aprendizado profundo da Intel](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Esses novos tamanhos de VM terão um armazenamento local 50% maior, bem como um melhor IOPS de disco local para leitura e gravação em comparação com os tamanhos [Ev3/Esv3](./ev3-esv3-series.md) com [VMs Gen2](./linux/generation-2.md)

## <a name="edv4-series"></a>Série Edv4

Os tamanhos da série Edv4 são executados nos processadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Os tamanhos de máquina virtual Edv4 contam com até 504 GiB de RAM, além de um armazenamento SSD local grande e rápido (de até 2.400 GiB). Essas máquinas virtuais são ideais para aplicativos empresariais com uso intensivo de memória e aplicativos que se beneficiam de armazenamento local de alta velocidade e baixa latência. Você pode anexar armazenamentos em disco SSDs Standard e HDDs Standard às VMs Edv4. 

ACU: 195 - 210

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4/4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8/10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8/30000 |


## <a name="edsv4-series"></a>Série Edsv4

Os tamanhos da série Edsv4 são executados nos processadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Os tamanhos de máquina virtual Edsv4 contam com até 504 GiB de RAM, além de um armazenamento SSD local grande e rápido (de até 2.400 GiB). Essas máquinas virtuais são ideais para aplicativos empresariais com uso intensivo de memória e aplicativos que se beneficiam de armazenamento local de alta velocidade e baixa latência.

ACU: 195 a 210

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120(50) | 3200/48 | 2/1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242(100) | 6400/96 | 2/2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485(200) | 12800/192 | 4/4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968(400) | 25600/384 | 8/8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211(500)  | 32000/480  | 8/10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936(800) | 51200/768  | 8/16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8/24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8/30000 |

<sup>1</sup> [Tamanhos limitados de núcleos disponíveis)](./windows/constrained-vcpu.md).


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
