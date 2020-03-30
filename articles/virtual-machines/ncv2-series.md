---
title: Série NCv2 - Máquinas Virtuais Azure
description: Especificações para vMs da série NCv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 3643fbabef08d890ce121d41a9bc1eb40c88459d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273923"
---
# <a name="ncv2-series"></a>Série NCv2

VMs da série NCv2 têm a tecnologia de GPUs [NVIDIA Tesla P100](https://www.nvidia.com/data-center/tesla-p100/). Essas GPUs podem fornecer desempenho computacional 2 vezes maior que da série NC. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho HPC tradicionais como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Monte Carlo, entre outros. Além das GPUs, as VMs da série NCv2 também são alimentadas por CPUs Intel Xeon E5-2690 v4 (Broadwell).

A configuração NC24rs v2 fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

> [!IMPORTANT]
> Para esta série VM, a cota de vCPU (núcleo) em sua assinatura é inicialmente definida como 0 em cada região. [Solicite um aumento da cota de vCPU](../azure-supportability/resource-manager-core-quotas-request.md) para esta série em uma [região disponível](https://azure.microsoft.com/regions/services/).
>
| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo throughput de disco sem cache: IOPS/MBps | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = um cartão P100.

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
