---
title: Detectar vazamento de memória - Azure Application Insights Smart Detection
description: Monitorar aplicativos com o Azure Application Insights para possíveis perdas de memória.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 85d138518dfb1313a810657016e9fe3143887b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671691"
---
# <a name="memory-leak-detection-preview"></a>Detecção de perda de memória (visualização)

O Application Insights analisa automaticamente o consumo de memória de cada processo em seu aplicativo e avisa sobre possíveis perdas de memória ou maior consumo de memória.

Este recurso não exige nenhuma configuração especial, diferente de [configurar contadores de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) para seu aplicativo. Ele está ativo quando seu aplicativo gera telemetria de contadores de desempenho de memória suficiente (por exemplo, Bytes particulares).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia este tipo de notificação de detecção inteligente?
Uma notificação típica seguirá um aumento consistente no consumo de memória por um longo período de tempo (algumas horas), em um ou mais processos e/ou em um ou mais computadores, que fazem parte do seu aplicativo. Algoritmos de aprendizado de máquina são usados para detectar o aumento do consumo de memória que corresponde ao padrão de um vazamento de memória.

## <a name="does-my-app-really-have-a-problem"></a>Meu aplicativo definitivamente tem um problema?
Não, uma notificação não significa que seu aplicativo definitivamente tem um problema. Embora os padrões de perda de memória geralmente indiquem um problema de aplicativo, esses padrões podem ser típicos para seu processo específico, ou podem ter uma justificativa de negócios natural e podem ser ignorados.

## <a name="how-do-i-fix-it"></a>Como corrigi-la?
As notificações incluem informações de diagnóstico para oferecer suporte no processo de análise de diagnóstico:
1. **Triagem.** A notificação mostra o aumento na quantidade de memória (em GB) e o intervalo de tempo em que a memória aumentou. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Escopo.** Quantas máquinas apresentaram o padrão de perda de memória? Quantas exceções foram acionadas durante a potencial perda de memória? Essas informações podem ser obtidas na notificação.
3. **Diagnosticar.** A detecção contém o padrão de perda de memória, mostrando o consumo de memória do processo ao longo do tempo. Você também pode usar o relatórios de vinculação para dar suporte a informações e itens relacionados para ajudá-lo a diagnosticar o problema.
