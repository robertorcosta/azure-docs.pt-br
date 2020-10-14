---
title: Desempenho de máquina virtual e disco
description: Saiba mais sobre como as VMs e seus discos anexados funcionam em conjunto para o desempenho
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 88cf9c28a9d325d617f4b049015f0cd238a2fb31
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016409"
---
# <a name="virtual-machine-and-disk-performance"></a>Desempenho de máquina virtual e disco
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites em cache de máquina virtual desarmazenado versus
 As máquinas virtuais que são habilitadas para o armazenamento Premium e o cache de armazenamento Premium habilitado têm dois limites de largura de banda de armazenamento diferentes. Vamos continuar examinando a máquina virtual Standard_D8s_v3 como um exemplo. Aqui está a documentação na [série Dsv3](../dv3-dsv3-series.md) e na Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Vamos executar um teste de parâmetro de comparação nessa VM e na combinação de disco que criará a atividade de e/s e você poderá aprender tudo sobre como fazer o benchmark de e/s de armazenamento no Azure [aqui](disks-benchmarks.md). Na ferramenta de benchmarking, você pode ver que a combinação de VM e disco é capaz de alcançar 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]