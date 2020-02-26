---
title: Tamanhos de VM do Azure-uso geral | Microsoft Docs
description: Lista os diferentes tamanhos de finalidade geral disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como taxa de transferência de armazenamento e largura de banda de rede para tamanhos desta série.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: d8a13d9013b2ad526c8e332cf3ea74e04f253d98
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597704"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Tamanhos das Máquinas Virtuais de uso geral

Os tamanhos de VM para uso geral fornecem uma relação de CPU para memória equilibrada. Ideal para teste e desenvolvimento, bancos de dados pequenos a médios e servidores Web de tráfego baixo a médio. Este artigo fornece informações sobre as ofertas para computação de uso geral.

- As VMs da [série Av2](av2-series.md) podem ser implantadas em uma variedade de tipos de hardware e processadores. As VMs da série A possuem configurações de memória e de desempenho de CPU mais adequadas para cargas de trabalho de entrada, como desenvolvimento e teste. O tamanho é limitado, com base no hardware, para oferecer desempenho de processador consistente para a instância em execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual. Os exemplos de casos de uso incluem servidores de desenvolvimento e teste, servidores Web de tráfego baixo, bancos de dados pequenos a médios, servidores para prova de conceito e repositórios de código.

- [Série B expansível](sizes-b-series-burstable.md) As VMs são ideais para cargas de trabalho que não precisam do desempenho total da CPU continuamente, como servidores Web, bancos de dados pequenos e ambientes de desenvolvimento e teste. Normalmente, essas cargas de trabalho têm requisitos de desempenho expansíveis. A série B fornece esses clientes a possibilidade de comprar um tamanho VM com um preço consciência da linha de base de desempenho que permite que a instância VM criar créditos quando a VM é menor que o desempenho de base. Quando a VM tiver acumulado crédito, poderá disparar acima da linha de base da VM usando até 100% da CPU quando seu aplicativo requer o maior desempenho de CPU.

- As [séries Dav4 e Dasv4](dav4-dasv4-series.md) são novos tamanhos que utilizam o processador de 2.35 GHz EPYC<sup>TM</sup> 7452 de AMD em uma configuração multi-THREADED com até 256 MB de cache L3, que dedica 8 GB desse cache L3 a cada 8 núcleos aumentando as opções de clientes para executar suas cargas de trabalho de uso geral. As séries Dav4 e Dasv4 têm as mesmas configurações de memória e disco que a D & série Dsv3.

- A [série DCv2](dcv2-series.md) pode ajudar a proteger a confidencialidade e a integridade de seus dados e código enquanto eles são processados na nuvem pública. Essas máquinas têm o respaldo da última geração de processador E-2288G Intel XEON com tecnologia de SGX. Com a tecnologia Intel Turbo Boost, essas máquinas podem ir até 5.0 GHz. As instâncias da série DCv2 permitem que os clientes criem aplicativos baseados em enclave seguros para proteger seu código e seus dados enquanto eles estão em uso.

- [Dv2 e Dsv2-Series](dv2-dsv2-series.md) As VMs, um acompanhamento da série D original, apresenta uma CPU mais potente e configuração ideal de CPU para memória, tornando-as adequadas para a maioria das cargas de trabalho de produção. A série Dv2 é de cerca de 35% mais rápida do que a série D. A série Dv2 é executada no Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) com a tecnologia Intel Turbo Boost 2,0. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

- [Dv3 e Dsv3-Series](dv3-dsv3-series.md) As VMs são executadas no Intel® Xeon® 8171M 2.1 GHz (Skylake), no Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) em uma configuração de hiperthread, fornecendo uma melhor proposta de valor para as cargas de trabalho de uso geral. A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites de rede e disco em uma base por núcleo foram ajustados para alinhar com a mudança para o hyperthreading. A série Dv3 não tem mais os tamanhos de VM de memória alta da série D/Dv2, elas foram movidas para o Ev3 de memória otimizada e para a [série Esv3](ev3-esv3-series.md).

Exemplos de casos de uso da série D incluem aplicativos de nível empresarial, bancos de dados relacionais, cache na memória e análise.

## <a name="other-sizes"></a>Outros tamanhos

- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.