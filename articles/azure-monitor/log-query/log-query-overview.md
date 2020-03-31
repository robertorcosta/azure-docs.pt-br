---
title: Visão geral das consultas de log no Azure Monitor | Microsoft Docs
description: Responde perguntas comuns relacionadas a consultas de log e faz com que você comece a usá-las.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670110"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Visão geral das consultas de log no Azure Monitor
As consultas de log ajudam você a aproveitar totalmente o valor dos dados coletados no [Azure Monitor Logs](../platform/data-platform-logs.md). Uma linguagem de consulta poderosa permite que você junte dados de várias tabelas, agregue grandes conjuntos de dados e realize operações complexas com código mínimo. Praticamente qualquer pergunta pode ser respondida e a análise realizada desde que os dados de suporte sejam coletados, e você entenda como construir a consulta certa.

Alguns recursos no Azure Monitor, como [insights](../insights/insights-overview.md) e [soluções processam](../insights/solutions-inventory.md) dados de log sem expô-lo às consultas subjacentes. Para aproveitar totalmente outros recursos do Azure Monitor, você deve entender como as consultas são construídas e como você pode usá-las para analisar interativamente os dados no Azure Monitor Logs.

Use este artigo como ponto de partida para aprender sobre consultas de log no Azure Monitor. Responde a perguntas comuns e fornece links para outras documentações que fornecem mais detalhes e aulas.

## <a name="how-can-i-learn-how-to-write-queries"></a>Como posso aprender a escrever consultas?
Se você quiser pular direto para as coisas, você pode começar com os seguintes tutoriais:

- [Comece com o Log Analytics no Azure Monitor](get-started-portal.md).
- [Comece com consultas de log no Azure Monitor](get-started-queries.md).

Depois de ter o básico para baixo, passe por várias lições usando seus próprios dados ou dados do nosso ambiente de demonstração, começando com: 

- [Trabalhe com cadeias de caracteres nas consultas de log no Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Que idioma as consultas de log usam?
O Azure Monitor Logs é baseado no [Azure Data Explorer](/azure/data-explorer)e as consultas de log são escritas usando a mesma linguagem de consulta kusto (KQL). Esta é uma linguagem rica projetada para ser fácil de ler e autor, e você deve ser capaz de começar a usá-lo com o mínimo de orientação.

Consulte [a documentação KQL do Azure Data Explorer](/azure/kusto/query) para obter documentação completa sobre KQL e referência em diferentes funções disponíveis.<br>
Consulte [Iniciar consultas de log no Azure Monitor](get-started-queries.md) para um rápido passo a passo do idioma usando dados do Azure Monitor Logs.
Consulte as diferenças de linguagem de consulta de log do [Azure Monitor](data-explorer-difference.md) para pequenas diferenças na versão do KQL usada pelo Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Quais dados estão disponíveis para registrar consultas?
Todos os dados coletados no Azure Monitor Logs estão disponíveis para recuperar e analisar em consultas de log. Diferentes fontes de dados gravarão seus dados em diferentes tabelas, mas você pode incluir várias tabelas em uma única consulta para analisar dados em várias fontes. Quando você constrói uma consulta, você começa determinando quais tabelas têm os dados que você está procurando, então você deve ter pelo menos uma compreensão básica de como os dados no Azure Monitor Logs são estruturados.

Consulte [Fontes do Azure Monitor Logs](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), para obter uma lista de diferentes fontes de dados que preenchem os logs do Monitor do Azure.<br>
Consulte [Estrutura do Azure Monitor Logs](logs-structure.md) para obter uma explicação de como os dados estão estruturados.

## <a name="what-does-a-log-query-look-like"></a>Como é uma consulta de log?
Uma consulta pode ser tão simples quanto um único nome de tabela para recuperar todos os registros dessa tabela:

```Kusto
Syslog
```

Ou poderia filtrar para registros específicos, resumi-los e visualizar os resultados em um gráfico:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Para uma análise mais complexa, você pode recuperar dados de várias tabelas usando uma junta para analisar os resultados em conjunto.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Mesmo que você não esteja familiarizado com o KQL, você deve ser capaz de pelo menos descobrir a lógica básica que está sendo usada por essas consultas. Eles começam com o nome de uma tabela e, em seguida, adicionam vários comandos para filtrar e processar esses dados. Uma consulta pode usar qualquer número de comandos, e você pode escrever consultas mais complexas à medida que você se familiariza com os diferentes comandos KQL disponíveis.

Consulte [Comece com consultas de log no Azure Monitor](get-started-queries.md) para obter um tutorial sobre consultas de log que introduz o idioma e funções comuns, .<br>


## <a name="what-is-log-analytics"></a>O que é o Log Analytics?
O Log Analytics é a principal ferramenta no portal Azure para escrever consultas de log e analisar interativamente seus resultados. Mesmo que uma consulta de log seja usada em outro lugar no Azure Monitor, você normalmente escreverá e testará a consulta primeiro usando o Log Analytics.

Você pode iniciar o Log Analytics de vários lugares no portal Azure. O escopo dos dados disponíveis no Log Analytics é determinado pela forma como você os inicia. Consulte [O Escopo de Consulta](scope.md) para obter mais detalhes.

- Selecione **Logs** no menu **Azure Monitor** ou no menu **Log Analytics workspaces.**
- Selecione **'Análise'** na página **Visão geral** de um aplicativo Application Insights.
- Selecione **Logs** no menu de um recurso do Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Consulte [Comece com o Log Analytics no Azure Monitor](get-started-portal.md) para um passo a passo tutorial do Log Analytics que introduz vários de seus recursos.

## <a name="where-else-are-log-queries-used"></a>Onde mais estão as consultas de log usadas?
Além de trabalhar interativamente com consultas de log e seus resultados no Log Analytics, as áreas no Monitor do Azure onde você usará consultas incluem o seguinte:

- **Regras de alerta** As [Regras de alerta](../platform/alerts-overview.md) identificam proativamente os problemas dos dados no workspace.  Cada regra de alerta é baseada em uma pesquisa de logs que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se um alerta deve ser criado.
- **Painéis.** Você pode fixar os resultados da consulta em um [painel do Azure](../learn/tutorial-logs-dashboards.md), que permite visualizar os dados de log e de métrica em conjunto e, opcionalmente, compartilhar com outros usuários do Azure.
- **Modos de exibição.**  Você pode criar visualizações de dados a serem incluídas em painéis de usuários com [Designer de Exibição](../platform/view-designer.md).  As consultas de logs fornecem os dados usados por [blocos](../platform/view-designer-tiles.md) e [blocos de visualização](../platform/view-designer-parts.md) em cada exibição.  
- **Exportar.**  Ao importar dados de log do Azure Monitor para o Excel ou o [Power BI](../platform/powerbi.md), você cria uma consulta de logs para definir os dados a serem exportados.
- **Powershell.** Você pode executar um script PowerShell a partir de uma linha de comando ou de um runbook azure Automation que usa [get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) para recuperar dados de log do Azure Monitor.  Esse cmdlet requer uma consulta para determinar os dados a serem recuperados.
- **API de Logs do Azure Monitor.**  A [API de Logs do Azure Monitor](https://dev.loganalytics.io) permite que qualquer cliente da API REST recupere dados de log do workspace.  A solicitação de API inclui uma consulta que é executada no Azure Monitor para determinar os dados a serem recuperados.


## <a name="next-steps"></a>Próximas etapas
- Faça um [tutorial sobre como usar o Log Analytics no portal Azure](get-started-portal.md).
- Passe por um [tutorial sobre como escrever consultas.](get-started-queries.md)
