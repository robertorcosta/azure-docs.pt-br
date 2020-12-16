---
title: Série NVv4
description: Especificações para as VMs da série NVv4.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 0b7d200ef39a6b4da6af33a0a9b9314635c3db33
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586424"
---
# <a name="nvv4-series"></a>Série NVv4 

As máquinas virtuais da série NVv4 são alimentadas por GPUs [AMD Radeon instinto MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) e CPUs AMD EPYC 7V12 (Roma). Com o NVv4-Series, o Azure está introduzindo máquinas virtuais com GPUs parciais. Escolha a máquina virtual de tamanho certo para aplicativos gráficos acelerados de GPU e áreas de trabalho virtuais começando em 1/8 de uma GPU com 2 buffer de quadros GiB para uma GPU completa com 16 buffer de quadros GiB. Atualmente, as máquinas virtuais NVv4 dão suporte apenas ao sistema operacional convidado do Windows.

<br>

[ACU](acu.md): 230-260<br>
[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1<br>
Rede acelerada: com suporte<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Número máximo de NICs/largura de banda de rede esperada (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> as VMs da série NVv4 apresentam a tecnologia de vários threads simultâneos da AMD

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série NVv4 do Azure que executam o Windows, os drivers de GPU AMD devem ser instalados.

Para instalar os drivers do AMD GPU manualmente, consulte [instalação do driver da GPU AMD da série N para Windows](./windows/n-series-amd-driver-setup.md) para sistemas operacionais, Drivers, instalação e etapas de verificação com suporte.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
