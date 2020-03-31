---
title: Use o Azure Log Analytics para examinar os logs de firewall do aplicativo Gateway Web
description: Este artigo mostra como você pode usar o Azure Log Analytics para examinar os logs do Firewall do Aplicativo Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: e1bc3b58f425b374e4cae1da6e9800579e503f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516585"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Usar o Log Analytics para examinar os logs de firewall do aplicativo Web do Gateway de Aplicativo

Uma vez que o WAF do Gateway de aplicativo esteja operacional, você pode habilitar logs para inspecionar o que está acontecendo com cada solicitação. Os logs de firewall fornecem uma visão do que o WAF está avaliando, combinando e bloqueando. Com o Log Analytics, você pode examinar os dados dentro dos logs de firewall para dar ainda mais insights. Para obter mais informações sobre a criação de um espaço de trabalho do Log Analytics, consulte [Criar um espaço de trabalho do Log Analytics no portal Azure](../../azure-monitor/learn/quick-create-workspace.md). Para obter mais informações sobre consultas de log, consulte [Visão geral das consultas de log no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importar logs WAF

Para importar seus logs de firewall no Log Analytics, consulte [Back-end health, logs de diagnóstico e métricas para O Gateway de Aplicativos](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging). Quando você tem os logs de firewall no espaço de trabalho do Log Analytics, você pode visualizar dados, escrever consultas, criar visualizações e adicioná-los ao painel do portal.

## <a name="explore-data-with-examples"></a>Explorar dados com exemplos

Para visualizar os dados brutos no registro de firewall, você pode executar a seguinte consulta:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Isso será semelhante à seguinte consulta:

![Consulta do Log Analytics](../media/log-analytics/log-query.png)

Você pode detalhar os dados e traçar gráficos ou criar visualizações a partir daqui. Veja as seguintes consultas como ponto de partida:

### <a name="matchedblocked-requests-by-ip"></a>Solicitações combinadas/bloqueadas por IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Solicitações combinadas/bloqueadas por URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Principais regras combinadas

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Top 5 grupos de regras compatíveis

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Adicione ao seu painel

Depois de criar uma consulta, você pode adicioná-la ao seu painel.  Selecione o **Pin para fazer** o painel no canto superior direito do espaço de trabalho de análise de log. Com as quatro consultas anteriores fixadas em um painel de exemplo, estes são os dados que você pode ver rapidamente:

![Painel](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Próximas etapas

[Saúde back-end, registros de diagnóstico e métricas para o Gateway de aplicativos](../../application-gateway/application-gateway-diagnostics.md)