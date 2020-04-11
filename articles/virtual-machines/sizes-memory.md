---
title: Tamanhos Azure VM - Memória | Microsoft Docs
description: Lista os diferentes tamanhos otimizados de memória disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
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
ms.author: jonbeck
ms.openlocfilehash: 0936e3049b2bc23e2f98244f29c962f6db65b8df
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115264"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais com GPU otimizadas para memória

Os tamanhos de VM otimizados para memória oferecem uma taxa de memória alta para CPU que são ideais para servidores de banco de dados relacionais, caches médio a grande e análises in-memory. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho neste agrupamento.

- [As séries Dv2 e DSv2](dv2-dsv2-series-memory.md), uma continuação da série D original, possui uma CPU mais poderosa. A série Dv2 é cerca de 35% mais rápida que a série D. Ele roda nos&reg; processadores&reg; Intel Xeon 8171M 2.1&reg; GHz&reg; (Skylake) ou Intel Xeon E5-2673&reg; v4&reg; 2.3 GHz (Broadwell) ou intel Xeon E5-2673 v3 2.4 GHz (Haswell) e com a Tecnologia Intel Turbo Boost 2.0. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

    As séries Dv2 e DSv2 são ideais para aplicações que exigem vCPUs mais rápidos, melhor desempenho de armazenamento temporário ou têm maiordemanda de memória. Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.

- As [séries Eav4 e Easv4](eav4-easv4-series.md) utilizam o processador AMD 2.35Ghz<sup>TM</sup> 7452 em uma configuração multi-threaded com cache de até 256MB L3, aumentando as opções para executar a maioria das cargas de trabalho otimizadas de memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que a série Ev3 & Série Esv3.

- O processador [Intel](ev3-esv3-series.md) &reg; Xeon&reg; 8171M 2.1 GHz (Skylake) ou&reg; o Processador&reg; Intel Xeon E5-2673 v4 2.3 GHz (Broadwell) em uma configuração hiper-threaded, fornecendo uma melhor proposta de valor para a maioria das cargas de trabalho de propósito geral, e trazendo o Ev3 para o alinhamento com os VMs de propósito geral da maioria das outras nuvens. A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU), enquanto os limites de disco e rede foram ajustados por núcleo para se alinhar em relação ao movimento para hiper-threading. A série Ev3 é o acompanhamento até os tamanhos de VM de memória alta das famílias D/Dv2.

- A [série M](m-series.md) oferece uma alta contagem de vCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Também é ideal para bancos de dados extremamente grandes ou outros aplicativos que se beneficiam de altas contagens de vCPU e grandes quantidades de memória.

- A [série Mv2](mv2-series.md) oferece a maior contagem de vCPU (até 416 vCPUs) e a maior memória (até 11,4 TiB) de qualquer VM na nuvem. É ideal para bancos de dados extremamente grandes ou outros aplicativos que se beneficiam de altas contagens de vCPU e grandes quantidades de memória.

A Computação do Azure oferece tamanhos de máquina virtual Isolada, para um tipo de hardware específico e dedicada a um único cliente. Esses tamanhos de máquina virtual são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de outros clientes, para cargas de trabalho que envolvem elementos como requisitos normativos e de conformidade. Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais Isoladas usando [o Suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Consulte as páginas de famílias de máquinas virtuais abaixo para ver suas opções isoladas de VM.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Otimizado para computação](sizes-compute.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.