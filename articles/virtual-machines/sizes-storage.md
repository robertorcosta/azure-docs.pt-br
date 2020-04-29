---
title: Tamanhos de VM do Azure – armazenamento | Microsoft Docs
description: Lista os diferentes tamanhos otimizados para armazenamento disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como taxa de transferência de armazenamento e largura de banda de rede para tamanhos desta série.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77913329"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais otimizados para armazenamento

Os tamanhos de VM otimizados para armazenamento oferecem alta taxa de transferência e e/s de disco e são ideais para Big data, SQL, bancos de dados NoSQL, data warehousing e grandes bancos de dados transacionais.  Os exemplos incluem Cassandra, MongoDB, Cloudera e Redis. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como taxa de transferência de armazenamento local e largura de banda de rede para cada tamanho otimizado.

A [Lsv2-Series](lsv2-series.md) apresenta alta taxa de transferência, baixa latência, armazenamento de NVMe local mapeado diretamente em execução no [processador AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) com um aumento principal de 2.55 GHz e um aumento máximo de 3,0 GHz. As VMs da série Lsv2 vêm em tamanhos de 8 para vCPU 80 em uma configuração de vários thread simultânea.  Há 8 GiB de memória por vCPU e um dispositivo de NVMe SSD M.2 de 1,92 TB por 8 vCPUs, com até 19,2 TB (10x1.92TB) disponível no L80s v2.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.

Saiba como otimizar o desempenho nas máquinas virtuais da série Lsv2 para [Windows](windows/storage-performance.md) ou [Linux](linux/storage-performance.md).
