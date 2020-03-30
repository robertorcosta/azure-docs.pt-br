---
title: Solução de problemas a escala automática do Azure
description: Rastreando problemas com o autoscaling do Azure usado em malha de serviço, máquinas virtuais, web apps e serviços em nuvem.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751341"
---
# <a name="troubleshooting-azure-autoscale"></a>Solução de problemas a escala automática do Azure
 
A escala automática do Azure Monitor ajuda você a ter a quantidade certa de recursos em execução para lidar com a carga em seu aplicativo. Ele permite adicionar recursos para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão ociosos. Você pode dimensionar com base em um cronograma, data-hora fixa ou métrica de recurso escolhida. Para obter mais informações, consulte [Visão geral de escala automática](autoscale-overview.md).

O serviço de autoescala fornece métricas e registros para entender quais ações de escala ocorreram e a avaliação das condições que levaram a essas ações. Você pode encontrar respostas para perguntas como:

- Por que minha escala de serviço ou dentro?
- Por que meu serviço não foi escalado?
- Por que uma ação em escala automática falhou?
- Por que uma ação em escala automática está levando tempo para ser dimensionada?
  
## <a name="autoscale-metrics"></a>Métricas de autoescala

A autoescala fornece [quatro métricas](metrics-supported.md#microsoftinsightsautoscalesettings) para entender seu funcionamento. 

- **Valor métrico observado** - O valor da métrica que você escolheu para tomar a ação de escala, como visto ou computado pelo motor de escala automática. Como uma única configuração de escala automática pode ter várias regras e, portanto, várias fontes métricas, você pode filtrar usando "fonte métrica" como uma dimensão.
- **Limite métrico** - O limiar definido para tomar a ação de escala. Como uma única configuração de escala automática pode ter várias regras e, portanto, várias fontes métricas, você pode filtrar usando "regra métrica" como uma dimensão.
- **Capacidade Observada** - O número ativo de instâncias do recurso de destino visto pelo motor Autoscale.
- **Ações de dimensionamento iniciadas** - O número de ações de expansão e redução horizontal iniciadas pelo mecanismo de dimensionamento automático. Você pode filtrar por escala versus escala em ações.

Você pode usar o [Metrics Explorer](metrics-getting-started.md) para mapear as métricas acima em um só lugar. O gráfico deve mostrar:

  - a métrica real
  - a métrica vista/computada pelo motor de autoescala
  - o limiar para uma ação de escala
  - a mudança de capacidade 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Exemplo 1 - Analisar uma regra simples de escala automática 

Temos uma configuração de escala automática simples para um conjunto de escala de máquina virtual que:

- escala quando a porcentagem média da CPU de um conjunto é superior a 70% por 10 minutos 
- escalas quando a porcentagem da CPU do conjunto é inferior a 5% por mais de 10 minutos. 

Vamos rever as métricas do serviço de escala automática.
 
![Exemplo de cpu de definir em escala de máquina virtual](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Exemplo de cpu de definir em escala de máquina virtual](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Figura 1a - Métrica percentual da CPU para conjunto de escala de máquina virtual e a métrica de valor métrico observada para configuração de escala automática***

![Limiar métrico e capacidade observada](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Figura 1b - Limiar métrico e capacidade observada***

Na figura 1b, o **Limiar Métrico** (linha azul claro) para a regra de saída de escala é 70.  A **Capacidade Observada** (linha azul escura) mostra o número de instâncias ativas, que atualmente é de 3. 

> [!NOTE]
> Você precisará filtrar o **Limiar métrico** pela regra de escala de escala (aumento) da dimensão da regra do gatilho métrico para ver o limiar de saída de escala e pela escala na regra (diminuição). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Exemplo 2 - Autoscaling avançado para um conjunto de escala de máquina virtual

Temos uma configuração de escala automática que permite que um recurso de conjunto de escala de máquina virtual seja dimensionado com base em sua própria métrica **Outbound Flows**. Observe que a opção **de contagem de métricas por instância** para o limiar métrico é verificada. 

A regra de ação em escala é: 

Se o valor de Fluxo de **Saída por instância** for maior que 10, então o serviço de escala automática deve ser dimensionado por 1 instância. 

Neste caso, o valor métrico observado do motor de escala automática é calculado como o valor métrico real dividido pelo número de instâncias. Se o valor métrico observado for menor que o limiar, nenhuma ação de saída de escala será iniciada. 
 
![Exemplo de gráficos de métricas de escala automática de escala de máquina virtual](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Exemplo de gráficos de métricas de escala automática de escala de máquina virtual](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Figura 2 - Exemplo de gráficos de métricas de escala automática da escala da máquina virtual***

Na figura 2, você pode ver dois gráficos métricos. 

O gráfico no topo mostra o valor real da métrica **Outbound Flows.** O valor real é 6. 

O gráfico na parte inferior mostra alguns valores. 
 - O **valor métrico observado** (azul claro) é 3 porque há 2 instâncias ativas e 6 divididas por 2 é 3. 
 - A **Capacidade Observada** (roxo) mostra a contagem de ocorrências vista pelo motor de autoescala. 
 - O **Limiar Métrico** (verde claro) está definido como 10. 

Se houver regras de ação em várias escalas, você pode usar a opção de divisão ou a opção **adicionar filtro** no gráfico do explorador métrica para olhar a métrica por uma fonte ou regra específica. Para obter mais informações sobre a divisão de um gráfico métrico, consulte [Recursos avançados de gráficos métricos - divisão](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>Exemplo 3 - Entendendo eventos em escala automática

Na tela de configuração de escala automática, vá para a guia **Executar histórico** para ver as ações de escala mais recentes. A guia também mostra a mudança na **Capacidade Observada** ao longo do tempo. Para encontrar mais detalhes sobre todas as ações de escala automática, incluindo operações como configurações de atualização/exclusão de escala automática, visualize o registro de atividades e o filtro por operações de escala automática.

![Configurações de escala automática executam o histórico](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Logs de recursos de escala automática

Mesmo que qualquer outro recurso do Azure, o serviço de autoescala fornece [logs de recursos](platform-logs-overview.md). Existem duas categorias de logs.

- **Avaliações em escala automática** - O mecanismo de autoescala registra entradas de registro para cada avaliação de condições cada vez que faz uma verificação.  A entrada inclui detalhes sobre os valores observados das métricas, as regras avaliadas e se a avaliação resultou em uma ação de escala ou não.

- **Ações de escala de autoescala** - Os eventos de ação em escala de gerenciamento de mecanismos iniciados pelo serviço de autoescala e os resultados dessas ações de escala (sucesso, falha e quanto de dimensionamento ocorreu como visto pelo serviço de autoescala).

Como em qualquer serviço suportado pelo Azure Monitor, você pode usar [configurações de diagnóstico](diagnostic-settings.md) para roteirizar esses registros:

- ao seu espaço de trabalho do Log Analytics para análise detalhada
- para Hubs de Eventos e, em seguida, para ferramentas não-Azure
- para sua conta de armazenamento do Azure para arquivamento  

![Configurações de diagnóstico em escala automática](media/autoscale-troubleshoot/diagnostic-settings.png)

A imagem anterior mostra as configurações de diagnóstico em escala automática do portal Azure. Lá você pode selecionar a guia Diagnóstico/Registros de recursos e ativar a coleta e o roteamento do log. Você também pode executar a mesma ação usando modelos REST API, CLI, PowerShell, Resource Manager para Configurações de Diagnóstico, escolhendo o tipo de recurso como *Microsoft.Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Solução de problemas usando logs de escala automática 

Para obter a melhor experiência de solução de problemas, recomendamos o roteamento de seus logs para loganalytics de monitores do Azure (Log Analytics) através de um espaço de trabalho quando você cria a configuração de escala automática. Este processo é mostrado na imagem na seção anterior. Você pode validar melhor as avaliações e dimensionar as ações usando o Log Analytics.

Depois de configurar seus logs de escala automática a serem enviados para o espaço de trabalho do Log Analytics, você pode executar as seguintes consultas para verificar os logs. 

Para começar, tente esta consulta para visualizar os registros de avaliação de autoescala mais recentes:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Ou tente a seguinte consulta para visualizar os registros de ação de escala mais recentes:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Use as seguintes seções para essas perguntas. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Uma ação em escala ocorreu que eu não esperava

Primeiro execute a consulta para ação em escala para encontrar a ação de escala na qual você está interessado. Se for a ação de escala mais recente, use a seguinte consulta:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Selecione o campo CorrelaçãoId no registro de ações de escala. Use o CorrelaçãoId para encontrar o registro de avaliação certo. A execução da consulta abaixo exibirá todas as regras e condições avaliadas levando a essa ação de escala.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Que perfil causou uma ação em escala?

Uma ação em escala ocorreu, mas você tem regras e perfis sobrepostos e precisa rastrear o que causou a ação. 

Encontre a correlaçãoId da ação de escala (conforme explicado no exemplo 1) e execute a consulta nos registros de avaliação para saber mais sobre o perfil.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Toda a avaliação do perfil também pode ser melhor entendida usando a seguinte consulta

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Uma ação em escala não ocorreu

Eu esperava uma ação em escala e isso não ocorreu. Pode não haver eventos de ação em escala ou logs.

Revise as métricas de escala automática se estiver usando uma regra de escala baseada em métricas. É possível que o **valor métrico observado** ou a capacidade **observada** não sejam o que você esperava que fossem e, portanto, a regra da escala não disparou. Você ainda veria avaliações, mas não uma regra de escala. Também é possível que o tempo de resfriamento impediu que uma ação em escala ocorresse. 

 Revise os registros de avaliação de escala automática durante o período de tempo que você esperava que a ação de escala ocorresse. Reveja todas as avaliações que fez e por que decidiu não desencadear uma ação em escala.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Falha na ação de escala

Pode haver um caso em que o serviço de autoescala tomou a ação de escala, mas o sistema decidiu não escalar ou não completar a ação de escala. Use esta consulta para encontrar as ações de escala com falha.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Crie regras de alerta para ser notificado sobre ações ou falhas em escala automática. Você também pode criar regras de alerta para ser notificado em eventos de escala automática.

## <a name="schema-of-autoscale-resource-logs"></a>Esquema de logs de recursos em escala automática

Para obter mais informações, consulte [registros de recursos em escala automática](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Próximas etapas
Leia informações sobre [as melhores práticas de autoescala.](autoscale-best-practices.md) 
