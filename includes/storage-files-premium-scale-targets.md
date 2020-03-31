---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180031"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites adicionais de nível de compartilhamento de arquivos premium

|Área  |Destino  |
|---------|---------|
|Aumento/diminuição de tamanho mínimo    |1 GiB      |
|IOPS de linha de base    |1 IOPS por GiB, até 100.000|
|IOPS estourando    |3x IOPS por GiB, até 100.000|
|Taxa de egresso         |60 MiB/s + 0,06 * GiB provisionado        |
|Taxa de ingressa| 40 MiB/s + 0,04 * GiB provisionado |

#### <a name="file-level-limits"></a>Limites de nível de arquivo

|Área  |Arquivo premium  |Arquivo padrão |
|---------|---------|---------|
|Tamanho                  |1 TiB         |1 TiB|
|Max IOPS por arquivo     |5.000         |1,000|
|Alças simultâneas    |2.000         |2.000|
|Saída  |300 MiB/seg|      Consulte os valores padrão de throughput do arquivo|
|Entrada  |200 MiB/segundo| Consulte os valores padrão de throughput do arquivo|
|Produtividade| Consulte os valores de ingress/egressos de arquivos premium| Até 60 MiB/s|
