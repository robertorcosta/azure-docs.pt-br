---
title: Série NVv4-máquinas virtuais do Azure
description: Especificações para as VMs da série NVv4.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 3b1deceb8d5c6e2a22254b2719449e5adb069def
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565390"
---
# <a name="nvv4-series-preview"></a>Série NVv4 (versão prévia)

As máquinas virtuais da série NVv4 são alimentadas por GPUs [AMD Radeon instinto MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) e CPUs AMD EPYC 7V12 (Roma). Com o NVv4-Series, o Azure está introduzindo máquinas virtuais com GPUs parciais. Escolha a máquina virtual de tamanho certo para aplicativos gráficos acelerados de GPU e áreas de trabalho virtuais começando em 1/8 de uma GPU com 2 buffer de quadros GiB para uma GPU completa com 16 buffer de quadros GiB. Atualmente, as máquinas virtuais NVv4 dão suporte apenas ao sistema operacional convidado do Windows.

[Inscreva-se e tenha acesso a essas máquinas durante a visualização](https://aka.ms/nvv4signup).
<br>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> as VMs da série NVv4 apresentam a tecnologia de vários threads simultâneos da AMD

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série N do Azure que executam o Windows, os drivers NVIDIA ou AMD GPU devem ser instalados.

A [extensão de Driver Nvidia GPU](/extensions/hpccompute-gpu-windows.md) instala drivers NVIDIA CUDA ou Grid apropriados em uma VM da série N do Windows. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](/extensions/hpccompute-gpu-windows.md) para saber quais são os sistemas operacionais compatíveis e as etapas de implantação. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](/extensions/overview.md).

Se você optar por instalar os drivers NVIDIA GPU manualmente, consulte [instalação do driver de GPU da série N para Windows](/windows/n-series-driver-setup.md) para sistemas operacionais, Drivers, instalação e etapas de verificação com suporte.

Para instalar os drivers do AMD GPU manualmente, consulte [instalação do driver da GPU AMD da série N para Windows](/windows/n-series-amd-driver-setup.md) para sistemas operacionais, Drivers, instalação e etapas de verificação com suporte.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.