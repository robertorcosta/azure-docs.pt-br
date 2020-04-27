---
title: Visão geral da VM da série HB – máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre o suporte de visualização para o tamanho da VM da série HB no Azure.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707770"
---
# <a name="hb-series-virtual-machines-overview"></a>Visão geral das máquinas virtuais da série HB

A maximização do desempenho do aplicativo HPC (computação de alto desempenho) no AMD EPYC requer uma abordagem cuidadosa de localidade de memória e colocação de processo. Abaixo, descrevemos a arquitetura AMD EPYC e nossa implementação de ti no Azure para aplicativos HPC. Usaremos o termo "pNUMA" para fazer referência a um domínio NUMA físico e "vNUMA" para fazer referência a um domínio NUMA virtualizado.

Fisicamente, uma série HB é 2 * 32-Core EPYC 7551 CPUs para um total de 64 núcleos físicos. Esses 64 núcleos são divididos em 16 domínios do pNUMA (8 por soquete), cada um dos quais são quatro núcleos e conhecidos como "CPU complexa" (ou "CCX"). Cada CCX tem seu próprio cache L3, que é como um sistema operacional verá um limite de pNUMA/vNUMA. Um par de CCXs adjacentes compartilha o acesso a dois canais de DRAM física (32 GB de DRAM em servidores da série HB).

Para fornecer espaço para o hipervisor do Azure operar sem interferir na VM, reservamos o domínio pNUMA físico 0 (o primeiro CCX). Em seguida, atribuímos domínios pNUMA 1-15 (as unidades de CCX restantes) para a VM. A VM verá:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`núcleos por VM

A VM, em si, não sabe que pNUMA 0 não foi fornecido a ela. A VM compreende pNUMA 1-15 como vNUMA 0-14, com 7 vNUMA em vSocket 0 e 8 vNUMA no vSocket 1. Embora isso seja assimétrico, seu sistema operacional deve inicializar e operar normalmente. Posteriormente neste guia, instruímos a melhor forma de executar aplicativos MPI neste layout NUMA assimétrica.

A fixação do processo funcionará em VMs da série HB porque expõemos o silício subjacente como está para a VM convidada. É altamente recomendável fixar o processo para desempenho e consistência ideais.

Veja mais sobre arquitetura [AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas de vários chips](https://bit.ly/2GpQIMb) no LinkedIn. Para obter informações mais detalhadas, consulte o [Guia de ajuste do HPC para processadores AMD EPYC](https://bit.ly/2T3AWZ9).

O diagrama a seguir mostra a diferenciação de núcleos reservada para o hipervisor do Azure e a VM da série HB.

![Segregação de núcleos reservada para a VM do Azure hypervisor e da série HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de HW                | VM da série HB                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 60 (SMT desabilitado)                |
| CPU                              | AMD EPYC 7551 *                   |
| Frequência de CPU (não AVX)          | ~ 2,55 GHz (único + todos os núcleos)   |
| Memória                           | 4 GB/núcleo (total de 240)            |
| Disco local                       | 700 GB NVMe                      |
| Infiniband                       | 100 GB EDR Mellanox ConnectX-5 * * |
| Rede                          | 50 GB de Ethernet (40 GB utilizáveis) Azure Second Gen SmartNIC * * * |

## <a name="software-specifications"></a>Especificações de software

| Especificações de SW           |VM da série HB           |
|-----------------------------|-----------------------|
| Tamanho máximo do trabalho MPI            | 6000 núcleos (100 conjuntos de dimensionamento de máquinas virtuais) 12000 núcleos (200 conjuntos de dimensionamento de máquinas virtuais)  |
| Suporte a MPI                 | MVAPICH2, OpenMPi, MPICH, plataforma MPI, Intel MPI  |
| Estruturas adicionais       | Comunicação unificada X, libfabric, PGAS |
| Suporte do armazenamento do Azure       | STD + Premium (máximo de 4 discos) |
| Suporte do so para SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Suporte do Azure CycleCloud    | Sim                         |
| Suporte do lote do Azure         | Sim                         |

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os tamanhos de VM do HPC para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) no Azure.

* Saiba mais sobre o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
