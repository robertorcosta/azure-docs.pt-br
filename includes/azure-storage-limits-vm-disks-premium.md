---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "67171911"
---
**Discos de máquina virtual Premium não gerenciados: limites por conta**

| Grupos | Limite padrão |
| --- | --- |
| Capacidade total do disco por conta |35 TB |
| Capacidade total de instantâneos por conta |10 TB |
| Largura de banda máxima por conta (entrada + saída)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*entrada* refere-se a todos os dados de solicitações enviadas para uma conta de armazenamento. *Saída* refere-se a todos os dados de respostas recebidas de uma conta de armazenamento.

**Discos de máquina virtual Premium não gerenciados: limites por disco**

| Tipo de disco de armazenamento Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Tamanho do disco |128 GiB |512 GiB |1\.024 GiB (1 TB) |2\.048 GiB (2 TB)|4\.095 GiB (4 TB)|
| IOPS máximo por disco |500 |2\.300 |5\.000 |7\.500 |7\.500 |
| Taxa de transferência máxima por disco |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Número máximo de discos por conta de armazenamento |280 |70 |35 | 17 | 8 |

**Discos de máquina virtual Premium não gerenciados: limites por VM**

| Grupos | Limite padrão |
| --- | --- |
| IOPS máximo por VM |80.000 IOPS com VM GS5 |
| Taxa de transferência máxima por VM |2\.000 MB/s com VM GS5 |

