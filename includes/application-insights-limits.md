---
title: incluir arquivo
description: incluir arquivo
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: eda50bb9f65591cd837b7e74e9d783464de43367
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520673"
---
Há alguns limites quanto ao número de métricas e eventos por aplicativo, ou seja, por chave de instrumentação. Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

| Recurso | Limite padrão | Observação
| --- | --- | --- |
| Total de dados por dia | 100 GB | Você pode reduzir os dados ao definir um limite. Caso precise de mais dados, é possível aumentar o limite até 1.000 GB. Para capacidades maiores que 1.000 GB, envie um email para AIDataCap@microsoft.com.
| Limitação | 32.000 eventos/s | O limite é medido em um minuto.
| Logs de retenção de dados | [De 30 a 730 dias](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Este recurso é para [Logs](../articles/azure-monitor/log-query/log-query-overview.md).
| Métricas de retenção de dados | 90 dias| Este recurso é para [Metrics Explorer](../articles/azure-monitor/platform/metrics-charts.md).
| Retenção de resultados detalhados do [Teste de disponibilidade de várias etapas](../articles/azure-monitor/app/availability-multistep.md) | 90 dias | Esse recurso fornece resultados detalhados de cada etapa.
| Tamanho máximo do item de telemetria | 64 KB |
| Máximo de itens de telemetria por lote | 64 k |
| Tamanho dos nomes de propriedade e métrica | 150 | Veja [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Tamanho da cadeia de caracteres do valor da propriedade | 8\.192  | Veja [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Comprimento da mensagem de rastreamento e de exceção | 32.768  | Veja [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Contagem de [testes de disponibilidade](../articles/azure-monitor/app/monitor-web-app-availability.md) por aplicativo | 100 |
| Retenção de dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) | 5 dias |
| Dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) enviados por dia | 10 GB |

Para obter mais informações, consulte [sobre preços e cotas no Application Insights](../articles/azure-monitor/app/pricing.md).