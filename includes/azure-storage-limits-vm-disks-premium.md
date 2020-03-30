---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334582"
---
**Discos de máquina virtual não gerenciados premium: limites por conta**

| Recurso | Limite |
| --- | --- |
| Capacidade total do disco por conta |35 TB |
| Capacidade total de instantâneos por conta |10 TB |
| Largura de banda máxima por conta (ingresss + egress)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*Ingress* refere-se a todos os dados de solicitações que são enviados para uma conta de armazenamento. *Egress* refere-se a todos os dados de respostas recebidas de uma conta de armazenamento.

**Discos de máquina virtual não gerenciados premium: limites por disco**

| Tipo de disco de armazenamento Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Tamanho do disco |128 GiB |512 GiB |1.024 GiB (1 TB) |2.048 GiB (2 TB)|4.095 GiB (4 TB)|
| IOPS máximo por disco |500 |2.300 |5.000 |7.500 |7.500 |
| Throughput máximo por disco |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Número máximo de discos por conta de armazenamento |280 |70 |35 | 17 | 8 |

**Discos de máquina virtual não gerenciados premium: limites per-VM**

| Recurso | Limite |
| --- | --- |
| IOPS máximo por VM |80.000 IOPS com VM GS5 |
| Throughput máximo por VM |2.000 MB/seg com VM GS5 |

