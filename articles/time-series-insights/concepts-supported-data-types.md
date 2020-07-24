---
title: Tipos de dados com suporte-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre os tipos de dados com suporte no Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c2e70a4f5cdbbc7a5a408138c3ec832cc831cf33
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046567"
---
# <a name="supported-data-types"></a>Tipos de dados com suporte

A tabela a seguir lista os tipos de dados com suporte pelo Azure Time Series Insights Gen2

| Tipo de dados | Descrição | Exemplo | Nome da coluna de propriedade em parquet
|---|---|---|---|
| **bool** | Um tipo de dados com um dos dois estados: `true` ou `false`. | "isquestionável": verdadeiro | isQuestionable_bool
| **datetime** | Representa um momento no tempo, geralmente expresso como uma data e hora do dia. Expresso no formato [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). As propriedades DateTime são sempre armazenadas no formato UTC. Os deslocamentos de fuso horário, se formatados corretamente, serão aplicados e, em seguida, o valor armazenado em UTC. Consulte [esta](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) seção para obter mais informações sobre a propriedade timestamp do ambiente e os deslocamentos de DateTime | "eventProcessedLocalTime": "2020-03-20T09:03:32.8301668 Z" | eventProcessedLocalTime_datetime 
| **double** | Um número de precisão dupla de 64 bits  | "valor": 31, 482941 | value_double
| **long** | Um inteiro de 64 bits assinado  | "valor": 31 | value_long
| **cadeia de caracteres** | Os valores de texto devem consistir em UTF-8 válido. |  "site": "DIM_MLGGG" | site_string
| **dinâmico** | Um tipo complexo (não primitivo) que consiste em uma matriz ou um recipiente de Propriedades (dicionário). No momento, apenas as matrizes JSON em cadeias de primitivos ou matrizes de objetos que não contêm a ID TS ou as propriedades (s) de carimbo de data/hora serão armazenadas como dinâmicas. Leia este [artigo](./concepts-json-flattening-escaping-rules.md) para entender como os objetos serão mesclados e as matrizes podem ser revertidas. As propriedades de carga armazenadas como esse tipo podem ser acessadas por meio do Azure Time Series Insights Explorer Gen2 e da API de consulta GetEvents. |  "valores": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * Seu ambiente de Azure Time Series Insights Gen2 é fortemente tipado. Se dispositivos ou marcas enviarem dados integral e não integral, os valores de Propriedade do dispositivo serão armazenados em duas colunas duplas e longas separadas e a [função de adesão ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) deverá ser usada ao fazer chamadas à API e definir suas expressões de variáveis de modelo de série temporal.

#### <a name="objects-and-arrays"></a>Objetos e matrizes

Você pode enviar tipos complexos, como objetos e matrizes, como parte da carga do evento. Os objetos aninhados serão mesclados e as matrizes serão armazenadas como `dynamic` ou achatadas para produzir vários eventos, dependendo da sua configuração de ambiente e da forma JSON. Para saber mais sobre as [regras de saída e mesclagem JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Próximas etapas

* Leia as [regras de saída e mesclagem JSON](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados. 

* Entenda as limitações de [taxa de transferência](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente

* Saiba mais sobre [fontes de eventos](concepts-streaming-ingestion-event-sources.md) para ingerir dados de streaming.
