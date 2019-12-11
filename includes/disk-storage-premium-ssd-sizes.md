---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c88cba57ed987074c0dde3c8b251f54393667740
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "75002671"
---
| Tamanhos de unidades SSD Premium | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamanho do disco em GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1\.024 | 2\.048 | 4\.096 | 8\.192 | 16.384 | 32.767 |
| IOPS por disco | 120 | 120 | 120 | 120 | 240 | 500 | 1\.100 | 2\.300 | 5\.000 | 7\.500 | 7\.500 | 16.000 | 18.000 | 20.000 |
| Taxa de transferência por disco | 25 MiB/segundo | 25 MiB/segundo | 25 MiB/segundo | 25 MiB/segundo | 50 MiB/segundo | 100 MiB/segundo | 125 MiB/segundo | 150 MiB/segundo | 200 MiB/segundo | 250 MiB/segundo | 250 MiB/segundo| 500 MiB/segundo | 750 MiB/segundo | 900 MiB/segundo |
| IOPS de intermitência máxima por disco** | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 |
| Taxa de transferência de intermitência máxima por disco** | 170 MiB/segundo | 170 MiB/segundo | 170 MiB/segundo | 170 MiB/segundo | 170 MiB/segundo | 170 MiB/segundo | 170 MiB/segundo | 170 MiB/segundo |
| Duração máxima de intermitência** | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |

\*Indica um tamanho de disco que está em versão prévia; para obter informações de disponibilidade na região, consulte [Novos tamanhos de disco: Gerenciados e não gerenciados](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).

\*\*Indica um recurso que está em versão prévia. Consulte [Intermitência de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) para obter mais informações.
