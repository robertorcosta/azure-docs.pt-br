---
title: Desempenho de máquina virtual e disco-Linux
description: Saiba mais sobre como as máquinas virtuais e seus discos anexados funcionam em conjunto para o desempenho no Linux.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591847"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>Desempenho de máquina virtual e disco (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites em cache de máquina virtual desarmazenado versus
As máquinas virtuais habilitadas para armazenamento Premium e cache de armazenamento Premium têm dois limites de largura de banda de armazenamento diferentes. Vamos examinar a máquina virtual Standard_D8s_v3 como um exemplo. Aqui está a documentação sobre a [série Dsv3](../dv3-dsv3-series.md) e o Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Vamos executar um teste de benchmark nessa máquina virtual e na combinação de disco que cria a atividade de e/s. Para saber como avaliar o parâmetro de e/s de armazenamento no Azure, confira [avaliar seu aplicativo no armazenamento em disco do Azure](disks-benchmarks.md). Na ferramenta de benchmarking, você pode ver que a combinação de VM e disco pode atingir 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
