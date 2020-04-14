---
title: Tamanhos de VM azure - Computação otimizada | Microsoft Docs
description: Lista os diferentes tamanhos otimizados de computação disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como throughput de armazenamento e largura de banda de rede para tamanhos nesta série.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e08d593f641c42f9ad605fda013206e70a34e52f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269628"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Compute tamanhos de máquinas virtuais otimizadas

Os tamanhos de VM otimizados da computação têm uma alta relação CPU-memória. Esses tamanhos são bons para servidores web de tráfego médio, aparelhos de rede, processos em lote e servidores de aplicativos. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs. Ele também inclui informações sobre throughput de armazenamento e largura de banda de rede para cada tamanho neste agrupamento.

A [série Fsv2](fsv2-series.md) é baseada no processador Intel® Xeon® Platinum 8168. Possui uma velocidade de relógio Turbo de 3,4 GHz e uma frequência turbo de núcleo único de 3,7 GHz. As instruções ® Intel AVX-512 são novas em processadores Intel Scalable. Essas instruções fornecem até um aumento de desempenho 2X para cargas de trabalho de processamento de vetores em operações de ponto flutuante de precisão única e dupla. Em outras palavras, eles são muito rápidos para qualquer carga de trabalho computacional.

A um preço de lista por hora inferior, a série Fsv2 é o melhor valor de preço/desempenho no portfólio do Azure com base na ACU (Unidade de Computação do Azure) por vCPU.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Otimizado para memória](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
