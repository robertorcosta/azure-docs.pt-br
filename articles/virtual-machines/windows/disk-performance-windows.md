---
title: Desempenho de máquina virtual e disco-Windows
description: Saiba mais sobre como as máquinas virtuais e seus discos anexados funcionam em conjunto para o desempenho no Windows.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584112"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Desempenho de máquina virtual e disco (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites em cache de máquina virtual desarmazenado versus
 As máquinas virtuais que são habilitadas para o armazenamento Premium e o cache de armazenamento Premium habilitado têm dois limites de largura de banda de armazenamento diferentes. Vamos continuar examinando a máquina virtual Standard_D8s_v3 como um exemplo. Aqui está a documentação na [série Dsv3](../dv3-dsv3-series.md) e na Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Vamos executar um teste de parâmetro de comparação nessa VM e na combinação de disco que criará a atividade de e/s e você poderá aprender tudo sobre como fazer o benchmark de e/s de armazenamento no Azure [aqui](disks-benchmarks.md). Na ferramenta de benchmarking, você pode ver que a combinação de VM e disco é capaz de alcançar 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]