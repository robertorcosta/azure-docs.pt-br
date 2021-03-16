---
title: Série HBv3-máquinas virtuais do Azure
description: Especificações para as VMs da série HBv3.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 21576b6714c05e5a0ed703c7a1452103c2c4b355
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472809"
---
# <a name="hbv3-series"></a>Série HBv3

As VMs da série HBv3 são otimizadas para aplicativos HPC, como dinâmica de fluidos, análise de elementos finitos explícitos e implícitos, modelagem do clima, processamento sísmico, simulação de reservatório e simulação DPE. O recurso de VMs HBv3 até 120 AMD EPYC™ 7003-Series (Milão) núcleos de CPU, 448 GB de RAM e nenhum hyperthreading. As VMs da série HBv3 também fornecem 350 GB/s de largura de banda de memória, até 32 MB de cache L3 por núcleo, até 7 GB/s de desempenho de SSD de dispositivo de bloco e frequências de relógio de até 3,675 GHz. 

Todas as VMs da série HBv3 apresentam um cabeçalho HDR de 200 GB/s de rede NVIDIA para habilitar cargas de trabalho MPI de escala de supercomputador. Essas VMs são conectadas em uma árvore Fat sem bloqueio para desempenho de RDMA otimizado e consistente. A malha InfiniBand HDR também dá suporte ao roteamento adaptável e ao transporte conectado dinâmico (DCT, em outros transportes Standard RC e UD). Esses recursos aprimoram o desempenho, a escalabilidade e a consistência do aplicativo e seu uso é altamente recomendado.

[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): em breve<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): sem suporte <br>


|Tamanho |vCPU |Processador |Memória (GiB) |Largura de banda de memória GB/s |Frequência de CPU base (GHz) |Frequência de todos os núcleos (GHz, pico) |Frequência de núcleo único (GHz, pico) |Desempenho de RDMA (GB/s) |Suporte a MPI |Armazenamento temporário (GiB) |Discos de dados máximos |VNICs Ethernet máx. |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Todos |2 * 960 |32 |8 |
|Standard_HB120-96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Todos |2 * 960 |32 |8 |
|Standard_HB120-64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Todos |2 * 960 |32 |8 |
|Standard_HB120-32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Todos |2 * 960 |32 |8 |
|Standard_HB120-16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Todos |2 * 960 |32 |8 |


Saiba mais sobre:
- [Arquitetura subjacente e topologia de VM](./workloads/hpc/hbv3-series-overview.md)
- [Pilha de software com suporte](./workloads/hpc/hbv3-series-overview.md#software-specifications) , incluindo sistema operacional com suporte
- [Desempenho](./workloads/hpc/hbv3-performance.md) esperado da VM da série HBv3.

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.