---
title: Série NC - Azure Virtual Machines
description: Especificações para as VMs da série NC.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: eab7dfe79aa5cdf234c8bc9472387214f7df3563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164518"
---
# <a name="nc-series"></a>Série NC

As VMs da série NC são alimentadas pela placa [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) e pelo processador Intel Xeon E5-2690 v3 (Haswell). Os usuários podem processar os dados mais rapidamente aproveitando o CUDA para aplicativos de exploração de energia, simulações de falhas, renderização de traçados de raio, aprendizado profundo e muito mais. A configuração NC24r fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = metade de um cartão K80.

*Compatível com RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série Azure N, os drivers de GPU NVIDIA devem ser instalados.

A [Extensão de Driver de GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) para saber quais são os sistemas operacionais compatíveis e as etapas de implantação. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](./extensions/overview.md).

Se você optar por instalar os drivers de GPU NVIDIA manualmente, consulte [a configuração do driver gpu da série N para windows](./windows/n-series-driver-setup.md) ou [n-series GPU configuração para Linux](./linux/n-series-driver-setup.md) para sistemas operacionais, drivers, instalações e etapas de verificação suportadas.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
