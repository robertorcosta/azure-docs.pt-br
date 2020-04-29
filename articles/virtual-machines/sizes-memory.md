---
title: Tamanhos de VM do Azure-memória | Microsoft Docs
description: Lista os diferentes tamanhos otimizados para memória disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Isolamento de VM, VM isolada, isolamento, isolada
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 5a20e9c64b6ef948167333b54b16b34e84dc0e32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273572"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais com GPU otimizadas para memória

Os tamanhos de VM otimizados para memória oferecem uma taxa de memória alta para CPU que são ideais para servidores de banco de dados relacionais, caches médio a grande e análises in-memory. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho neste agrupamento.

- A [Dv2 e a série DSv2](dv2-dsv2-series-memory.md), uma continuação da série D original, apresenta uma CPU mais potente. A série Dv2 é de cerca de 35% mais rápida do que a série D. Ele é executado nos processadores&reg; Intel&reg; Xeon 8171M 2,1 GHz (Skylake) ou Intel&reg; Xeon&reg; E5-2673 v4 2,3 GHz (Broadwell) ou Intel&reg; Xeon&reg; E5-2673 v3 2,4 GHz (Haswell) e com a tecnologia Intel Turbo Boost 2,0. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

    As séries Dv2 e DSv2 são ideais para aplicativos que exigem mais rapidez vCPUs, melhor desempenho de armazenamento temporário ou têm maior demanda de memória. Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.

- A [Eav4 e a série Easv4](eav4-easv4-series.md) utilizam o processador EPYC<sup>TM</sup> 7452 de 2.35 GHz em uma configuração multi-threaded com até 256 MB de cache L3, aumentando as opções para executar a maioria das cargas de trabalho com otimização de memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que o Ev3 & série Esv3.

- A [Ev3 e a série Esv3](ev3-esv3-series.md) Intel&reg; Xeon&reg; 8171M 2,1 GHz (Skylake) ou o processador&reg; Intel&reg; Xeon E5-2673 v4 2,3 GHz (Broadwell) em uma configuração de hiperthread, fornecendo uma melhor proposta de valor para as cargas de trabalho de uso geral e trazendo a Ev3 para o alinhamento com as VMs de uso geral da maioria das outras nuvens. A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites de disco e rede foram ajustados por núcleo para ficarem alinhados com a mudança para o hyperthreading. A série Ev3 é o acompanhamento até os tamanhos de VM de memória alta das famílias D/Dv2.

- A [série M](m-series.md) oferece uma contagem alta de vCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TIB). Ele também é ideal para bancos de dados muito grandes ou outros aplicativos que se beneficiam de contagens vCPUs altas e grandes quantidades de memória.

- A [série Mv2](mv2-series.md) oferece a contagem de vCPU mais alta (até 416 vCPUs) e a maior memória (até 11,4 TIB) de qualquer VM na nuvem. É ideal para bancos de dados muito grandes ou outros aplicativos que se beneficiam de contagens vCPUs altas e grandes quantidades de memória.

A Computação do Azure oferece tamanhos de máquina virtual Isolada, para um tipo de hardware específico e dedicada a um único cliente. Esses tamanhos de máquina virtual são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de outros clientes, para cargas de trabalho que envolvem elementos como requisitos normativos e de conformidade. Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais Isoladas usando [o Suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Consulte as páginas para as famílias de máquinas virtuais abaixo para suas opções de VM isoladas.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
