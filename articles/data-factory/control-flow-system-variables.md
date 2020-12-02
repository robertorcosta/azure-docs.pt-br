---
title: Variáveis do sistema no Azure Data Factory
description: Este artigo descreve as variáveis do sistema com suporte pelo Azure Data Factory. Você pode usar essas variáveis em expressões ao definir entidades do Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 1780b4a64de349c1e272158fe6bfde9cab6f8369
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486038"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variáveis do sistema com suporte pelo Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve as variáveis do sistema com suporte pelo Azure Data Factory. Você pode usar essas variáveis em expressões ao definir entidades do Data Factory.

## <a name="pipeline-scope"></a>Escopo do pipeline
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no pipeline dp JSON.

| Nome da variável | Descrição |
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

| Nome da variável | Descrição |
| --- | --- |
| @trigger().scheduledTime |Hora em que o gatilho foi agendado para invocar a execução do pipeline. Por exemplo, para um gatilho disparado a cada cinco minutos, essa variável retornaria `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z` e `2017-06-01T22:30:00Z` respectivamente.|
| @trigger().startTime |Hora em que o gatilho **realmente** foi disparado para invocar a execução do pipeline. Por exemplo, para um gatilho disparado a cada cinco minutos, essa variável retornaria algo como `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z` e `2017-06-01T22:30:00.9935483Z` respectivamente. (Observação: o carimbo de data/hora é por padrão no formato ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Escopo do Gatilho de Janela em Cascata
Essas variáveis de sistema podem ser referenciadas em qualquer lugar no JSON do gatilho se ele for do tipo: "TumblingWindowTrigger."
(Observação: o carimbo de data/hora é por padrão no formato ISO 8601)

| Nome da variável | Descrição |
| --- | --- |
| @trigger().outputs.windowStartTime |Início da janela em que o gatilho foi agendado para invocar a execução do pipeline. Se o gatilho da janela em cascata tiver uma frequência "por hora", esse seria o horário no início da hora.|
| @trigger().outputs.windowEndTime |Fim da janela em que o gatilho foi agendado para invocar a execução do pipeline. Se o gatilho da janela em cascata tiver uma frequência "por hora", esse seria o horário no fim da hora.|
## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre como essas variáveis são usadas em expressões, consulte [Funções e linguagem de expressão](control-flow-expression-language-functions.md).
