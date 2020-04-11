---
title: Tamanhos azure VM - Propósito geral | Microsoft Docs
description: Lista os diferentes tamanhos de propósito geral disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como throughput de armazenamento e largura de banda de rede para tamanhos nesta série.
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
ms.openlocfilehash: 22826c32dc2aee0e580ec0b2a05c7eb8f08b7570
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115318"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Tamanhos das Máquinas Virtuais de uso geral

Os tamanhos de VM para uso geral fornecem uma relação de CPU para memória equilibrada. Ideal para teste e desenvolvimento, bancos de dados pequenos a médios e servidores Web de tráfego baixo a médio. Este artigo fornece informações sobre as ofertas para fins gerais de computação.

- As VMs [da série Av2](av2-series.md) podem ser implantadas em uma variedade de tipos de hardware e processadores. As VMs da série A possuem configurações de memória e de desempenho de CPU mais adequadas para cargas de trabalho de entrada, como desenvolvimento e teste. O tamanho é limitado, com base no hardware, para oferecer desempenho de processador consistente para a instância em execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual. Os exemplos de casos de uso incluem servidores de desenvolvimento e teste, servidores Web de tráfego baixo, bancos de dados pequenos a médios, servidores para prova de conceito e repositórios de código.

  > [!NOTE]
  > As VMs A8 – A11 estão previstas para a aposentadoria em 3/2021. Para obter mais informações, consulte [o Guia de Migração do HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [Série B explodida](sizes-b-series-burstable.md) As VMs são ideais para cargas de trabalho que não precisam do desempenho completo da CPU continuamente, como servidores web, pequenos bancos de dados e ambientes de desenvolvimento e teste. Normalmente, essas cargas de trabalho têm requisitos de desempenho expansíveis. A série B fornece esses clientes a possibilidade de comprar um tamanho VM com um preço consciência da linha de base de desempenho que permite que a instância VM criar créditos quando a VM é menor que o desempenho de base. Quando a VM tiver acumulado crédito, poderá disparar acima da linha de base da VM usando até 100% da CPU quando seu aplicativo requer o maior desempenho de CPU.

- [As séries Dav4 e Dasv4](dav4-dasv4-series.md) são novos tamanhos que utilizam o processador AMD 2.35Ghz<sup>TM</sup> 7452 em uma configuração multi-threaded com cache l3 de até 256 MB dedicando 8 MB desse cache L3 a cada 8 núcleos aumentando as opções do cliente para executar suas cargas de trabalho de uso geral. As séries Dav4 e Dasv4 têm as mesmas configurações de memória e disco que a série Dsv3 & Dsv3.

- A [série DCv2](dcv2-series.md) pode ajudar a proteger a confidencialidade e integridade de seus dados e códigos enquanto ele é processado na nuvem pública. Estas máquinas são apoiadas pela última geração do Processador Intel XEON E-2288G com tecnologia SGX. Com a tecnologia Intel Turbo Boost essas máquinas podem ir até 5.0GHz. As instâncias da série DCv2 permitem que os clientes construam aplicativos seguros baseados em enclave para proteger seu código e dados enquanto eles estão em uso.

- [Séries Dv2 e Dsv2](dv2-dsv2-series.md) As VMs, uma continuação da série D original, apresentam uma CPU mais poderosa e uma configuração ótima de CPU para memória, tornando-as adequadas para a maioria das cargas de trabalho de produção. A série Dv2 é cerca de 35% mais rápida que a série D. A série DV2 é executada nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com a Tecnologia Intel Turbo Boost 2.0. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

- [Séries Dv3 e Dsv3](dv3-dsv3-series.md) As VMs são executadas no Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou na Intel® Xeon® processadores E5-2673 v3 2.4 GHz (Haswell) em uma configuração hiper-threaded, fornecendo uma melhor proposta de valor para a maioria das cargas de trabalho de propósito geral. A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites de rede e disco em uma base por núcleo foram ajustados para alinhar com a mudança para o hyperthreading. A série Dv3 não tem mais os tamanhos de VM de alta memória da série D/Dv2, que foram movidos para a memória otimizada [sérieEv3 e Esv3](ev3-esv3-series.md).

Os casos de uso da série D incluem aplicativos de nível corporativo, bancos de dados relacionais, cache na memória e análises.

## <a name="other-sizes"></a>Outros tamanhos

- [Otimizado para computação](sizes-compute.md)
- [Otimizado para memória](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
