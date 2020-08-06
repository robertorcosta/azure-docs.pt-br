---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: c2bd10ab4c98fe2e77332c3cc2566ab2f0c7ad42
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841905"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites de nível de compartilhamento de arquivo Premium adicionais

|Área  |Destino  |
|---------|---------|
|Aumento/diminuição de tamanho mínimo    |1 GiB      |
|IOPS de linha de base    |1 IOPS por GiB, até 100.000|
|Intermitência de IOPS    |3x IOPS por GiB, até 100.000|
|Taxa de egresso         |60 MiB/s + 0, 6 * GiB provisionados        |
|Taxa de entrada| 40 MiB/s + 0, 4 * GiB provisionados |

#### <a name="file-level-limits"></a>Limites de nível de arquivo

|Área  |Arquivo Premium  |Arquivo padrão |
|---------|---------|---------|
|Tamanho                  |4 TiB         |1 TiB|
|IOPS máxima por arquivo     |5\.000         |1,000|
|Identificadores simultâneos    |2\.000         |2\.000|
|Saída  |300 MiB/s|      Consulte valores de taxa de transferência de arquivo padrão|
|Entrada  |200 MiB/segundo| Consulte valores de taxa de transferência de arquivo padrão|
|Taxa de transferência| Consulte valores de entrada/saída do arquivo Premium| Até 60 MiB/s|
