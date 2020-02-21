---
title: Série DC-máquinas virtuais do Azure
description: Especificações para as VMs da série DC.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8f36220ab81c0fd9533bf2f025b801b37823da5f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493807"
---
# <a name="dc-series"></a>Série DC

A [série DC](#dc-series) é uma família de máquinas virtuais no Azure que ajuda a proteger a confidencialidade e a integridade de seus dados e código enquanto eles são processados na nuvem pública. Essas máquinas têm o respaldo do processador 2176G Intel XEON de 3.7 GHz com tecnologia SGX. Com a Tecnologia Intel Turbo Boost, esses computadores podem atingir até 4,7 GHz. As instâncias da série DC permitem que os clientes criem aplicativos seguros baseados em enclave para proteger os códigos e os dados enquanto estiverem em uso.

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DC2s | 2 | 8  | 100 | 2 | 4000 / 32 (43) | 3200 /48 | 2 / 1500 |
| Standard_DC4s | 4 | 16 | 200 | 4 | 8000 / 64 (86) | 6400 /96 | 2 / 3000 |

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