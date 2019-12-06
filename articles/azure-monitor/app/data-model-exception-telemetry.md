---
title: Modelo de dados de telemetria de exceção do Aplicativo Azure insights
description: Modelo de dados do Application Insights para telemetria de exceções
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 7decac2e5e65ff7eedb374858fe15360b4a1b2c5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872614"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria de exceções: modelo de dados do Application Insights

Um [Application Insights](../../azure-monitor/app/app-insights-overview.md), uma instância da exceção representa uma exceção tratada ou sem tratamento que ocorreu durante a execução do aplicativo monitorado.

## <a name="problem-id"></a>ID do Problema

Identificador do local em que a exceção foi lançada no código. Usada para agrupamento de exceções. Normalmente, é uma combinação do tipo de exceção e uma função da pilha de chamadas.

Comprimento máximo: 1.024 caracteres

## <a name="severity-level"></a>Nível de gravidade

Nível de severidade de rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error` ou `Critical`.

## <a name="exception-details"></a>Detalhes da exceção

(A ser estendido)

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Próximos passos

- Consulte [modelo de dados](data-model.md) para modelo de dados e tipos do Application Insights.
- Aprenda a [diagnosticar exceções em seus aplicativos Web com o Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte do Application Insights.
