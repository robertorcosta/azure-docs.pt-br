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
ms.date: 07/23/2020
ms.openlocfilehash: 9d29d83ed92ee0fa2217bb1c27fbf6c2fbb3584c
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170869"
---
# <a name="supported-data-types"></a>Tipos de dados com suporte

A tabela a seguir lista os tipos de dados com suporte pelo Azure Time Series Insights Gen2

| Tipo de dados | Descrição | Exemplo | Nome da coluna de propriedade em parquet
|---|---|---|---|
| **bool** | Um tipo de dados com um dos dois estados: `true` ou `false`. | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | Representa um momento no tempo, geralmente expresso como uma data e hora do dia. Expresso no formato [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). As propriedades DateTime são sempre armazenadas no formato UTC. Os deslocamentos de fuso horário, se formatados corretamente, serão aplicados e, em seguida, o valor armazenado em UTC. Consulte [esta](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) seção para obter mais informações sobre a propriedade timestamp do ambiente e os deslocamentos de DateTime | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **duplo** | Um número de precisão dupla de 64 bits  | `"value": 31.0482941` | value_double
| **longo** | Um inteiro de 64 bits assinado  | `"value" : 31` | value_long
| **cadeia de caracteres** | Os valores de texto devem consistir em UTF-8 válido. Cadeias de caracteres nulas e vazias são tratadas da mesma. |  `"site": "DIM_MLGGG"` | site_string
| **dinâmico** | Um tipo complexo (não primitivo) que consiste em uma matriz ou um recipiente de Propriedades (dicionário). No momento, apenas as matrizes JSON em cadeias de primitivos ou matrizes de objetos que não contêm a propriedade de TS ID ou Timestamp (s) serão armazenadas como dinâmicas. Leia este [artigo](./concepts-json-flattening-escaping-rules.md) para entender como os objetos serão mesclados e as matrizes podem ser desvertidas. As propriedades de carga armazenadas como esse tipo podem ser acessadas por meio do Azure Time Series Insights Explorer Gen2 e da `GetEvents`   API de consulta. |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>Enviando tipos de dados mistos

Seu ambiente de Azure Time Series Insights Gen2 é fortemente tipado. Se dispositivos ou marcas enviarem dados de tipos diferentes para uma propriedade de dispositivo, os valores serão armazenados em duas colunas separadas e a [função de adesão ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions) deverá ser usada ao definir suas expressões de variáveis de modelo de série temporal em chamadas à API.

O Azure Time Series Insights Explorer oferece uma maneira de fazer a União automática das colunas separadas da mesma propriedade de dispositivo. No exemplo a seguir, o sensor envia uma `PresentValue` propriedade que pode ser longa ou dupla. Para consultar todos os valores armazenados (independentemente do tipo de dados) da `PresentValue` propriedade, escolha `PresentValue (Double | Long)` e as colunas serão Unidas para você.

[![União automática do Gerenciador](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objetos e matrizes

Você pode enviar tipos complexos, como objetos e matrizes, como parte da carga do evento. Os objetos aninhados serão mesclados e as matrizes serão armazenadas como `dynamic` ou achatadas para produzir vários eventos, dependendo da sua configuração de ambiente e da forma JSON. Para saber mais sobre as [regras de saída e mesclagem JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Próximas etapas

* Leia as [regras de saída e mesclagem JSON](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados.

* Entenda as limitações de [taxa de transferência](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente

* Saiba mais sobre [fontes de eventos](concepts-streaming-ingestion-event-sources.md) para ingerir dados de streaming.
