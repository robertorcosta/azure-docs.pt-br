---
title: Use reparticionamento para otimizar os trabalhos do Azure Stream Analytics
description: Este artigo descreve como usar o reparticionamento para otimizar trabalhos do Azure Stream Analytics que não podem ser paraparados.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732375"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Use reparticionamento para otimizar o processamento com o Azure Stream Analytics

Este artigo mostra como usar o reparticionamento para dimensionar sua consulta do Azure Stream Analytics para cenários que não podem ser totalmente [paraleleterizados](stream-analytics-scale-jobs.md).

Você pode não ser capaz de usar [a paralelização](stream-analytics-parallelization.md) se:

* Você não controla a chave de partição para o seu fluxo de entrada.
* Sua entrada de origem "pulveriza" em várias partições que mais tarde precisam ser mescladas.

O reparticionamento, ou remanete, é necessário quando você processa dados em um fluxo que não esteja preenchido de acordo com um esquema de entrada natural, como **PartitionId** for Event Hubs. Quando você repartição, cada fragmento pode ser processado de forma independente, o que permite que você dimensione linearmente seu pipeline de streaming.

## <a name="how-to-repartition"></a>Como repartição

Para repartição, use a palavra-chave **INTO** após uma **declaração PARTITION BY** em sua consulta. O exemplo a seguir particiona os dados por **DeviceID** em uma contagem de partição de 10. O hashing do **DeviceID** é usado para determinar qual partição deve aceitar qual subfluxo. Os dados são lavados independentemente para cada fluxo particionado, assumindo que a saída suporta gravações particionadas e tem 10 partições.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

A consulta de exemplo a seguir se junta a dois fluxos de dados reparticionados. Ao juntar dois fluxos de dados reparticionados, os fluxos devem ter a mesma chave de partição e contagem. O resultado é um fluxo que tem o mesmo esquema de partição.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

O esquema de saída deve corresponder à tecla de esquema de fluxo e contar para que cada subfluxo possa ser lavado independentemente. O fluxo também poderia ser mesclado e reparticionado novamente por um esquema diferente antes de dar descarga, mas você deve evitar esse método porque aumenta a latência geral do processamento e aumenta a utilização de recursos.

## <a name="streaming-units-for-repartitions"></a>Unidades de streaming para repartições

Experimente e observe o uso de recursos do seu trabalho para determinar o número exato de partições que você precisa. O número de unidades de [streaming (SU)](stream-analytics-streaming-unit-consumption.md) deve ser ajustado de acordo com os recursos físicos necessários para cada partição. Em geral, são necessários seis SUs para cada partição. Se houver recursos insuficientes atribuídos ao trabalho, o sistema só aplicará a repartição se beneficiar o trabalho.

## <a name="repartitions-for-sql-output"></a>Repartições para saída SQL

Quando seu trabalho usar o banco de dados SQL para saída, use repartição explícita para corresponder à contagem de partições ideal para maximizar o throughput. Uma vez que o SQL funciona melhor com oito escritores, reparticionar o fluxo para oito antes de dar descarga, ou em algum lugar mais upstream, pode beneficiar o desempenho do trabalho. 

Quando há mais de oito partições de entrada, a herança de entrada de esquema de particionamento pode não ser uma opção apropriada. Considere usar [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) em sua consulta para especificar explicitamente o número de escritores de saída. 

O exemplo a seguir é lido a partir da entrada, independentemente de ser naturalmente particionado, e repartições do fluxo dez vezes de acordo com a dimensão DeviceID e lava os dados para a saída. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Para obter mais informações, confira [Saída do Azure Stream Analytics para Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Aproveitar a paralelização de consultas no Azure Stream Analytics](stream-analytics-parallelization.md)
