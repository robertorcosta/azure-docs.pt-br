---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334582"
---
**Discos de máquina virtual Premium não gerenciados: limites por conta**

| Recurso | Limite |
| --- | --- |
| Capacidade total do disco por conta |35 TB |
| Capacidade total de instantâneos por conta |10 TB |
| Largura de banda máxima por conta (entrada + saída)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*entrada* refere-se a todos os dados de solicitações enviadas para uma conta de armazenamento. *Saída* refere-se a todos os dados de respostas recebidas de uma conta de armazenamento.

**Discos de máquina virtual Premium não gerenciados: limites por disco**

| Tipo de disco de armazenamento Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Tamanho do disco |128 GiB |512 GiB |1.024 GiB (1 TB) |2.048 GiB (2 TB)|4.095 GiB (4 TB)|
| IOPS máximo por disco |500 |2\.300 |5\.000 |7\.500 |7\.500 |
| Taxa de transferência máxima por disco |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Número máximo de discos por conta de armazenamento |280 |70 |35 | 17 | 8 |

**Discos de máquina virtual Premium não gerenciados: limites por VM**

| Recurso | Limite |
| --- | --- |
| IOPS máximo por VM |80.000 IOPS com VM GS5 |
| Taxa de transferência máxima por VM |2.000 MB/s com VM GS5 |

