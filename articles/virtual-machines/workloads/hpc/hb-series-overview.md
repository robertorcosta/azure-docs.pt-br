---
title: Visão geral da Série HB VM - Azure Virtual Machines | Microsoft Docs
description: Conheça o suporte de visualização para o tamanho vm da série HB no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707770"
---
# <a name="hb-series-virtual-machines-overview"></a>Visão geral das máquinas virtuais da série HB

Maximizar o desempenho do aplicativo de computação de alto desempenho (HPC) no AMD EPYC requer uma localização de memória de abordagem pensada e colocação de processos. Abaixo delineamos a arquitetura AMD EPYC e nossa implementação dela no Azure para aplicativos HPC. Usaremos o termo "pNUMA" para se referir a um domínio NUMA físico e "vNUMA" para se referir a um domínio NUMA virtualizado.

Fisicamente, uma série HB é 2 * 32 núcleos EPYC 7551 CPUs para um total de 64 núcleos físicos. Esses 64 núcleos estão divididos em 16 domínios pNUMA (8 por soquete), cada um dos quais são quatro núcleos e conhecidos como "Complexo de CPU" (ou "CCX"). Cada CCX tem seu próprio cache L3, que é como um sistema operacional verá um limite pNUMA/vNUMA. Um par de CCXs adjacentes compartilha o acesso a dois canais de DRAM físico (32 GB de DRAM em servidores da série HB).

Para dar espaço para o hipervisor Azure operar sem interferir com o VM, reservamos o domínio físico pNUMA 0 (o primeiro CCX). Em seguida, atribuímos domínios pNUMA 1-15 (as unidades CCX restantes) para a VM. A VM verá:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`núcleos por VM

A VM, em si, não sabe que pNUMA 0 não foi dado a ele. A VM entende pNUMA 1-15 como vNUMA 0-14, com 7 vNUMA no vSocket 0 e 8 vNUMA no vSocket 1. Embora isso seja assimétrico, o sistema operacional deve inicializar e operar normalmente. Mais tarde, neste guia, instruimos a melhor maneira de executar aplicativos MPI neste layout NUMA assimétrico.

A fixação de processos funcionará em VMs da série HB porque expomos o silício subjacente à VM convidada. Recomendamos fortemente a fixação do processo para o melhor desempenho e consistência.

Veja mais sobre [arquitetura AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas multi-chip](https://bit.ly/2GpQIMb) no LinkedIn. Para obter informações mais detalhadas, consulte o [Guia de Sintonia HPC para processadores AMD EPYC](https://bit.ly/2T3AWZ9).

O diagrama a seguir mostra a segregação de núcleos reservados para o Azure Hypervisor e o VM da série HB.

![Segregação de núcleos reservados para Azure Hypervisor e HB-series VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações hw                | Série HB VM                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 60 (SMT desativado)                |
| CPU                              | AMD EPYC 7551*                   |
| Frequência da CPU (não-AVX)          | ~2,55 GHz (único + todos os núcleos)   |
| Memória                           | 4 GB/núcleo (240 total)            |
| Disco local                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) Azure segunda gen SmartNIC*** |

## <a name="software-specifications"></a>Especificações de software

| Especificações sw           |Série HB VM           |
|-----------------------------|-----------------------|
| Max MPI Tamanho do trabalho            | 6000 núcleos (100 conjuntos de escala de máquinas virtuais) 12000 núcleos (200 conjuntos de escala de máquinas virtuais)  |
| Suporte ao MPI                 | MVAPICH2, OpenMPI, MPICH, Plataforma MPI, Intel MPI  |
| Estruturas adicionais       | Comunicação Unificada X, libfabric, PGAS |
| Suporte de armazenamento azure       | Std + Premium (max 4 discos) |
| Suporte ao Sistema Operacional para RDMA SRIOV   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Suporte ao Azure CycleCloud    | Sim                         |
| Suporte em lote azure         | Sim                         |

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os tamanhos de VM HPC para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) no Azure.

* Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
