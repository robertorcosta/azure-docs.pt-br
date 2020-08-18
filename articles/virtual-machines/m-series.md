---
title: Série M-máquinas virtuais do Azure
description: Especificações para as VMs da série M.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 79d0386e12b38eb6590b68ff959911ddc0a0ab5f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505539"
---
# <a name="m-series"></a>Série M

A série M oferece uma alta contagem de vCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Ela também é ideal para bancos de dados extremamente grandes ou outros aplicativos que se beneficiam de altas contagens de vCPU e de grandes quantidades de memória. Os tamanhos da série M têm suporte no Intel &reg; Xeon &reg; CPU E7-8890 v3 @ 2,50 GHz e no Intel &reg; Xeon &reg; Platinum 8280M (cascadey Lake).

A tecnologia Hyper-Threading da VM da série M &reg; .

ACU: 160 a 180

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

Migração ao Vivo: Sem suporte

Atualizações de preservação de memória: Sem suporte

Acelerador de gravação: [com suporte](./how-to-enable-write-accelerator.md)

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M8ms                    | 8   | 218,75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4|2000  |
| Standard_M16ms                   | 16  | 437,5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8|4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M32ms                   | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M64s <sup>1</sup>       | 64  | 1024   | 2.048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ls <sup>1</sup>      | 64  | 512    | 2.048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ms <sup>1</sup>      | 64  | 1792   | 2.048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M128s <sup>1</sup>    | 128 | 2.048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8|30000 |
| Standard_M128ms <sup>1, 2</sup>   | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8|30000 |
| Standard_M64 <sup>1</sup>        | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8|16000 |
| Standard_M64m <sup>1</sup>       | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8|16000 |
| Standard_M128 <sup>1</sup>     | 128 | 2.048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8|32000 |
| Standard_M128m <sup>1</sup>    | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8|32000 |

<sup>1</sup> mais de 64 vCPU requer um destes SOS convidados com suporte: Windows Server 2016, Ubuntu 16, 4 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7,3 ou Oracle Linux 7,3 com Lis 4.2.1.

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos: [tipos de disco](./linux/disks-types.md#ultra-disk)


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.