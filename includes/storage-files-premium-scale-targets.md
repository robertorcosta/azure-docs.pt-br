---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88057830"
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

|Área  |Arquivo padrão  |Arquivo Premium  |
|---------|---------|---------|
|Tamanho     |1 TiB         |4 TiB         |
|IOPS máxima por arquivo      |1,000         |5\.000         |
|Identificadores simultâneos     |2\.000         |2\.000         |
|Saída     |Consulte valores de taxa de transferência de arquivo padrão         |300 MiB/s         |
|Entrada     |Consulte valores de taxa de transferência de arquivo padrão         |200 MiB/segundo         |
|Produtividade     |Até 60 MiB/s         |Consulte valores de entrada/saída do arquivo Premium         |