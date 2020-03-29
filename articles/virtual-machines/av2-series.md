---
title: Série Av2 - Azure Virtual Machines
description: Especificações para as VMs da série Av2.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: fe27a1cc78d1f37d535f364c03803a5196090a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163940"
---
# <a name="av2-series"></a>Série Av2

As VMs da série Av2 podem ser implantadas em uma variedade de tipos de hardware e processadores. As VMs da série Av2 têm as configurações de desempenho e memória da CPU mais adequadas para cargas de trabalho de nível de entrada, como desenvolvimento e teste. O tamanho é acelerado para oferecer desempenho consistente do processador para a instância de execução, independentemente do hardware em que ele é implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual. Alguns casos de uso de exemplo incluem servidores de desenvolvimento e teste, servidores web de baixo tráfego, bancos de dados pequenos a médios, prova de conceitos e repositórios de código.

ACU: 100

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Throughput de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados máximos/throughput: IOPS | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2/250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |

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
