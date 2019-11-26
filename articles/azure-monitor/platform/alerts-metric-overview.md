---
title: Entender como funcionam os alertas de métrica no Azure Monitor.
description: Obtenha uma visão geral do que você pode fazer com alertas de métrica e como eles funcionam no Azure Monitor.
author: rboucher
ms.author: robb
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: b92b4233b6ecd8743f98f7f0dd13e07ad4c76c81
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484258"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Entender como funcionam os alertas de métrica no Azure Monitor

Os alertas de métrica no Azure Monitor funcionam com métricas multidimensionais. Essas métricas podem ser [métricas de plataforma](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [métricas personalizadas](../../azure-monitor/platform/metrics-custom-overview.md), [logs populares do Azure Monitor convertidos em métricas](../../azure-monitor/platform/alerts-metric-logs.md) e métricas do Application Insights. Os alertas de métrica verificam, em intervalos regulares, se as condições em uma ou mais séries temporais de métrica são verdadeiras e notificam você quando as condições são atendidas. Os alertas de métrica são baseados em estado, ou seja, eles só enviam notificações quando o estado é alterado.

## <a name="how-do-metric-alerts-work"></a>Como funcionam os alertas de métrica?

Você pode definir uma regra de alerta de métrica especificando um recurso de destino a ser monitorado, o nome, o tipo de condição (estático ou dinâmico) e a condição (um operador e um limite/sensibilidade) da métrica, além de um grupo de ações a disparar quando a regra de alerta é acionada. Os tipos de condição afetam a determinação dos limites. [Saiba mais sobre as opções de confidencialidade e tipo de condição dos Limites Dinâmicos](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Regra de alerta com o tipo de condição estática

Digamos que você tenha criado uma regra de alerta de métrica simples com limite estático da seguinte maneira:

- Recurso de destino (o recurso do Azure que você deseja monitorar): myVM
- Métrica: porcentagem de CPU
- Condition Type: Static
- Agregação de tempo (estatística que é executada sobre os valores brutos de métrica. Supported time aggregations are Min, Max, Avg, Total, Count): Average
- Period (The look back window over which metric values are checked): Over the last 5 mins
- Frequency (The frequency with which the metric alert checks if the conditions are met): 1 min
- Operator: Greater Than
- Limite: 70

Desde o momento em que a regra de alerta é criada, o monitor é executado a cada minuto, examina valores de métrica para os últimos 5 minutos e verifica se a média desses valores excede 70. Se a condição for atendida, ou seja, a média de porcentagem de CPU nos últimos 5 minutos exceder 70, a regra de alerta disparará uma notificação de ativação. Se você configurou um email ou uma ação de web hook no grupo de ações associado à regra de alerta, receberá uma notificação de ativação em ambos.

When you are using multiple conditions in one rule, the rule "ands" the conditions together.  That is, the alert fires when all the conditions in the alert evaluate as true and resolve when one of the conditions is no longer true. And example of this type of alert would be alert when "CPU higher than 90%" and "queue length is over 300 items". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Regra de alerta com o tipo de condição dinâmica

Digamos que você tenha criado uma regra de alerta de métrica simples com limites dinâmicos da seguinte maneira:

- Recurso de destino (o recurso do Azure que você deseja monitorar): myVM
- Métrica: porcentagem de CPU
- Condition Type: Dynamic
- Agregação de tempo (estatística que é executada sobre os valores brutos de métrica. Supported time aggregations are Min, Max, Avg, Total, Count): Average
- Period (The look back window over which metric values are checked): Over the last 5 mins
- Frequency (The frequency with which the metric alert checks if the conditions are met): 1 min
- Operator: Greater Than
- Sensitivity: Medium
- Look Back Periods: 4
- Number of Violations: 4

Depois que a regra de alerta for criada, o algoritmo de aprendizado de máquina de Limites Dinâmicos adquire dados históricos que estão disponíveis, calcula o limite que melhor atende ao padrão de comportamento da série de métricas e aprende continuamente com base em novos dados a fim de tornar o limite mais preciso.

Desde o momento em que a regra de alerta é criada, o monitor é executado a cada 1 minuto e examina valores de métrica nos últimos 20 minutos agrupados em períodos de 5 minutos, e verifica se a média dos valores de período em cada um dos 4 períodos excede o limite esperado. Se a condição é atendida, ou seja, a média percentual da CPU nos últimos 20 minutos (quatro períodos de 5 minutos) desviou do comportamento esperado quatro vezes, a regra de alerta aciona uma notificação ativada. Se você configurou um email ou uma ação de web hook no grupo de ações associado à regra de alerta, receberá uma notificação de ativação em ambos.

### <a name="view-and-resolution-of-fired-alerts"></a>Exibição e resolução de alertas acionados

Os exemplos acima de regras de alerta também podem ser exibidos no portal do Azure na folha **Todos os Alertas**.

Se o uso de "myVM" continuar acima do limite em verificações subsequentes, a regra de alerta não será acionada novamente até que as condições sejam resolvidas.

After some time, the usage on "myVM" comes back down to normal (goes below the threshold). A regra de alerta monitora a condição mais duas vezes, para enviar uma notificação de resolução. A regra de alerta envia uma mensagem de resolução/desativação quando a condição de alerta não é atendida por três períodos consecutivos para reduzir o excesso em caso de condições intermitentes.

Como a notificação de resolução é enviada por email ou webhooks, o status da instância do alerta (chamado de estado do monitor) no portal do Azure também é definido como resolvido.

### <a name="using-dimensions"></a>Usando dimensões

Os alertas de métrica no Azure Monitor também oferecem suporte ao monitoramento de várias combinações de valores de dimensões com uma regra. Vamos entender por que você pode usar várias combinações de dimensão com a ajuda de um exemplo.

Digamos que você tem um Plano do Serviço de Aplicativo para seu site. Você deseja monitorar o uso da CPU em várias instâncias de execução do site da Web/aplicativo. É possível fazer isso usando uma regra de alerta de métrica como mostrado abaixo:

- Recurso de destino: myAppServicePlan
- Métrica: porcentagem de CPU
- Condition Type: Static
- Dimensões
  - Instance = InstanceName1, InstanceName2
- Agregação de tempo: média
- Período: nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limite: 70

Como antes, essa regra monitora se a média de uso da CPU nos últimos 5 minutos ultrapassou 70%. No entanto, com a mesma regra você pode monitorar duas instâncias em execução no site. Cada instância será monitorada individualmente e você receberá notificações individualmente.

Digamos que você tenha um aplicativo Web que esteja sob grande demanda e é necessário adicionar mais instâncias. A regra acima ainda monitora apenas duas instâncias. No entanto, você pode criar uma regra como mostrado abaixo:

- Recurso de destino: myAppServicePlan
- Métrica: porcentagem de CPU
- Condition Type: Static
- Dimensões
  - Instância = *
- Agregação de tempo: média
- Período: nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limite: 70

Essa regra monitorará automaticamente todos os valores para a instância, ou seja, você pode monitorar suas instâncias conforme elas surgem sem precisar modificar a regra de alerta de métrica novamente.

Ao monitorar várias dimensões, a regra de alertas Limites Dinâmicos pode criar limites ajustados para centenas de séries de métricas por vez. Os Limites Dinâmicos resultam em menos regras de alerta a gerenciar e economia de tempo significativa com o gerenciamento e a criação de regras de alertas.

Digamos que você tenha um aplicativo Web com muitas instâncias e não sabe qual é o limite mais adequado. As regras acima sempre usam o limite de 70%. No entanto, você pode criar uma regra como mostrado abaixo:

- Recurso de destino: myAppServicePlan
- Métrica: porcentagem de CPU
- Condition Type: Dynamic
- Dimensões
  - Instância = *
- Agregação de tempo: média
- Período: nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Sensitivity: Medium
- Look Back Periods: 1
- Number of Violations: 1

Essa regra monitora se o uso médio da CPU nos últimos 5 minutos excede o comportamento esperado para cada instância. Com a mesma regra, você pode monitorar instâncias à medida que elas surgem, sem precisar modificar novamente a regra de alerta de métrica. Cada instância obtêm um limite que se ajusta ao padrão de comportamento da série de métricas e que muda continuamente com base nos novos dados para tornar o limite mais preciso. Como antes, cada instância é monitorada individualmente e você obtém notificações individualmente.

Aumentar os períodos de retrocesso e o número de violações também pode permitir filtrar alertas apenas para o alerta na sua definição de um desvio significativo. [Saiba mais sobre as opções avançadas de Limites Dinâmicos](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitoramento em escala usando alertas de métrica no Azure Monitor

Até agora, você viu como um único alerta de métrica pode ser usado para monitorar uma ou diversas séries de métricas temporais relacionadas a um único recurso do Azure. Em muitas situações, podemos querer a mesma regra de alerta aplicada a vários recursos. O Azure Monitor também dá suporte ao monitoramento de vários recursos com uma regra de alerta de métrica. Atualmente esse recurso só é suportado em máquinas virtuais. Além disso, um único alerta de métrica pode monitorar os recursos em uma única região do Azure.

Você pode especificar o escopo de monitoramento de um único alerta de métrica de uma destas três maneiras:

- como uma lista de máquinas virtuais em uma região do Azure dentro de uma assinatura
- todas as máquinas virtuais (em uma única região do Azure) em um ou mais grupos de recursos em uma assinatura
- todas as máquinas virtuais (em uma região do Azure) em uma assinatura

Criar regras de alerta de métrica que monitorem vários recursos é como [criar qualquer outro alerta de métrica](alerts-metric.md) que monitore um único recurso. A única diferença é que você selecionaria todos os recursos que quisesse monitorar. Você também pode criar essas regras por meio de [modelos do Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources). Você receberá notificações individuais para cada máquina virtual.

## <a name="typical-latency"></a>Latência típica

Para alertas de métrica, normalmente você será notificado em menos de 5 minutos se definir a frequência de regra de alerta para ser de 1 minuto. Em caso de carga pesada para sistemas de notificação, você poderá ter latência mais longa.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipos de recursos com suporte para alertas de métrica

Você pode encontrar a lista completa dos tipos de recursos com suporte neste [artigo](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Próximos passos

- [Saiba como criar, exibir e gerenciar alertas de métrica no Azure](alerts-metric.md)
- [Saiba como criar alertas de métrica usando modelos do Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Saiba mais sobre grupos de ação](action-groups.md)
- [Saiba mais sobre o tipo de condição de Limites Dinâmicos](alerts-dynamic-thresholds.md)
