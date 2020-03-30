---
title: Visão geral da Série HC VM - Azure Virtual Machines| Microsoft Docs
description: Saiba mais sobre o suporte de visualização para o tamanho da VM da série HC no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906750"
---
# <a name="hc-series-virtual-machine-overview"></a>Visão geral da máquina virtual da série HC

Maximizar o desempenho do aplicativo HPC nos processadores Escalados Intel Xeon requer uma abordagem pensada para a colocação de processos nesta nova arquitetura. Aqui, delineamos nossa implementação em VMs da série HC do Azure para aplicações HPC. Usaremos o termo "pNUMA" para se referir a um domínio NUMA físico e "vNUMA" para se referir a um domínio NUMA virtualizado. Da mesma forma, usaremos o termo "pCore" para se referir a núcleos de CPU físicos e "vCore" para se referir a núcleos de CPU virtualizados.

Fisicamente, um servidor HC é 2 * 24 núcleos Intel Xeon Platinum 8168 CPUs para um total de 48 núcleos físicos. Cada CPU é um único domínio pNUMA, e tem acesso unificado a seis canais de DRAM. As CPUs Intel Xeon Platinum possuem um cache L2 4x maior do que nas gerações anteriores (256 KB/core -> 1 MB/core), ao mesmo tempo em que reduzem o cache L3 em comparação com as CPUs Intel anteriores (2,5 MB/core -> 1.375 MB/núcleo).

A topologia acima também é a configuração do hipervisor da série HC. Para dar espaço para o hipervisor Azure operar sem interferir com a VM, reservamos pCores 0-1 e 24-25 (ou seja, os primeiros 2 pCores em cada tomada). Em seguida, atribuímos domínios pNUMA todos os núcleos restantes à VM. Assim, a VM verá:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`núcleos por VM

A VM não tem conhecimento de que pCores 0-1 e 24-25 não foram dados a ele. Assim, expõe cada vNUMA como se tivesse nativamente 22 núcleos.

As CPUs Intel Xeon Platinum, Gold e Silver também introduzem uma rede de malha 2D para comunicação dentro e externa ao soquete da CPU. Recomendamos fortemente a fixação do processo para o melhor desempenho e consistência. A fixação de processos funcionará em VMs da série HC porque o silício subjacente está exposto como está à VM convidada. Para saber mais, consulte [a arquitetura Intel Xeon SP](https://bit.ly/2RCYkiE).

O diagrama a seguir mostra a segregação de núcleos reservados para o Azure Hypervisor e o VM da série HC.

![Segregação de núcleos reservados para Azure Hypervisor e HC-series VM](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware          | VM série HC                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 44 (HT desativado)                 |
| CPU                              | Intel Xeon Platinum 8168*        |
| Frequência da CPU (não-AVX)          | 3,7 GHz (núcleo único), 2,7-3,4 GHz (todos os núcleos) |
| Memória                           | 8 GB/núcleo (352 no total)            |
| Disco local                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) Azure segunda gen SmartNIC*** |

## <a name="software-specifications"></a>Especificações de software

| Especificações de software     | VM série HC          |
|-----------------------------|-----------------------|
| Max MPI Tamanho do trabalho            | 13200 núcleos (300 VMs em um único VMSS com singlePlacementGroup=true) |
| Suporte ao MPI                 | MVAPICH2, OpenMPI, MPICH, Plataforma MPI, Intel MPI  |
| Estruturas adicionais       | Comunicação Unificada X, libfabric, PGAS |
| Suporte de armazenamento azure       | Std + Premium (max 4 discos) |
| Suporte ao Sistema Operacional para RDMA SRIOV   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Suporte ao Azure CycleCloud    | Sim                         |
| Suporte em lote azure         | Sim                         |

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os tamanhos de VM HPC para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) no Azure.

* Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
