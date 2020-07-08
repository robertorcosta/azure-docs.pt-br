---
title: Modelo de dados do Aplicativo Azure insights – telemetria de rastreamento
description: Modelo de dados do Application Insights para telemetria de rastreamento
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671946"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de rastreamento: modelo de dados do Application Insights

A telemetria de rastreamento (em [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representa instruções de rastreamento de estilo `printf` pesquisadas por texto. `Log4Net`, `NLog` e outras entradas do arquivo de log baseadas em texto são convertidas em instâncias desse tipo. O rastreamento não tem medidas como uma extensibilidade.

## <a name="message"></a>Mensagem

Mensagem de rastreamento.

Comprimento máximo: 32.768 caracteres

## <a name="severity-level"></a>Nível de severidade

Nível de severidade de rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error` ou `Critical`.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Próximas etapas

- [Explore os logs de rastreamento do .net em Application insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Explore os logs de rastreamento do Java em Application insights](../../azure-monitor/app/java-trace-logs.md).
- Consulte [modelo de dados](data-model.md) para modelo de dados e tipos do Application Insights.
- [Escrever telemetria personalizada de rastreamento](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte do Application Insights.
