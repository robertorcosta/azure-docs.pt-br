---
title: Série M - Azure Virtual Machines
description: Especificações para as VMs da série M.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/05/2019
ms.author: lahugh
ms.openlocfilehash: e8952218675493710cbdd1f92661e9a9c077f34c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164042"
---
# <a name="m-series"></a>Série M

A série M oferece uma alta contagem de vCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Também é ideal para bancos de dados extremamente grandes ou outros aplicativos que se beneficiam de altas contagens de vCPU e grandes quantidades de memória. Os tamanhos da série M são baseados na CPU Intel® Xeon® E7-8890 v3 @ 2.50GHz

Recurso da Série M VM Intel® Tecnologia de Hiper-Threading

ACU: 160 a 180

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

Acelerador de gravação: [com suporte](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de armazenamento em cache e armazenamento temporário: IOPS/MBps (tamanho de cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M8ms <sup>2</sup>       | 8   | 218,75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4/2000  |
| Standard_M16ms <sup>2</sup>      | 16  | 437,5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ms <sup>2</sup>      | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M64s                    | 64  | 1024   | 2.048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ls                   | 64  | 512    | 2.048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| <sup>Standard_M64ms 3</sup>      | 64  | 1792   | 2.048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>      | 128 | 2.048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1,2,3</sup> | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M64                     | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M64m                    | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>       | 128 | 2.048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>      | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |

<sup>1</sup> Mais de 64 vCPU's exigem um desses OSes convidados suportados: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 com LIS 4.2.1.

<sup>2</sup> Tamanhos de núcleo restritos disponíveis.

<sup>3</sup> A instância é isolada em hardware dedicado a um único cliente.

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
