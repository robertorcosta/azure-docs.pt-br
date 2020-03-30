---
title: Variáveis do sistema na fábrica de dados do Azure
description: Este artigo descreve as variáveis do sistema com suporte pelo Azure Data Factory. Você pode usar essas variáveis em expressões ao definir entidades do Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 0a5237336530d30c3801b13b910171e236e87a23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679283"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variáveis do sistema com suporte pelo Azure Data Factory
Este artigo descreve as variáveis do sistema com suporte pelo Azure Data Factory. Você pode usar essas variáveis em expressões ao definir entidades do Data Factory.

## <a name="pipeline-scope"></a>Escopo do pipeline
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no pipeline dp JSON.

| Nome de variável | Descrição |
| --- | --- |
| @pipeline().DataFactory |Nome do data factory em que a execução do pipeline está ocorrendo |
| @pipeline().Pipeline |Nome do pipeline |
| @pipeline().RunId | ID da execução do pipeline específica |
| @pipeline().TriggerType | Tipo de gatilho que invocou o pipeline (Manual, Agendador) |
| @pipeline().TriggerId| ID do gatilho que invoca o pipeline |
| @pipeline().TriggerName| Nome do gatilho que invoca o pipeline |
| @pipeline().TriggerTime| Hora em que o gatilho invocou o pipeline. A hora do gatilho é a hora do disparo real, não a hora agendada. Por exemplo, `13:20:08.0149599Z` é retornado em vez de `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Escopo do Gatilho de Agendamento
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no JSON do gatilho se ele for do tipo: "ScheduleTrigger."

| Nome de variável | Descrição |
| --- | --- |
| @trigger().scheduledTime |Hora em que o gatilho foi agendado para invocar a execução do pipeline. Por exemplo, para um gatilho disparado a cada cinco minutos, essa variável retornaria `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z` e `2017-06-01T22:30:00Z` respectivamente.|
| @trigger().startTime |Hora em que o gatilho **realmente** foi disparado para invocar a execução do pipeline. Por exemplo, para um gatilho disparado a cada cinco minutos, essa variável retornaria algo como `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z` e `2017-06-01T22:30:00.9935483Z` respectivamente. (Nota: O carimbo de tempo é padrão no formato ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Escopo do Gatilho de Janela em Cascata
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no JSON do gatilho se ele for do tipo: "TumblingWindowTrigger."
(Nota: O carimbo de tempo é padrão no formato ISO 8601)

| Nome de variável | Descrição |
| --- | --- |
| @trigger().outputs.windowStartTime |Início da janela em que o gatilho foi agendado para invocar a execução do pipeline. Se o gatilho da janela em cascata tiver uma frequência "por hora", esse seria o horário no início da hora.|
| @trigger().outputs.windowEndTime |Fim da janela em que o gatilho foi agendado para invocar a execução do pipeline. Se o gatilho da janela em cascata tiver uma frequência "por hora", esse seria o horário no fim da hora.|
## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre como essas variáveis são usadas em expressões, consulte [Funções e linguagem de expressão](control-flow-expression-language-functions.md).
