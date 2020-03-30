---
title: Monitoramento e registro de firewall de aplicativos web do Azure
description: Aprenda o WAF (Web Application Firewall) com o monitoramento e o registro do FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284136"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoramento e registro de firewall de aplicativos web do Azure 

O monitoramento e o registro do Azure Web Application Firewall (WAF) são fornecidos através do registro e integração com os logs do Azure Monitor e do Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

O WAF com o registro FrontDoor é integrado [ao Azure Monitor](../../azure-monitor/overview.md). O Azure Monitor permite rastrear informações de diagnóstico, incluindo alertas e logs waf. Você pode configurar o monitoramento do WAF dentro do recurso Front Door no portal na guia **Diagnósticos** ou através do serviço Azure Monitor diretamente.

Do portal Azure, vá para o tipo de recurso Front Door. Da guia **Monitorando**/**métricas** à esquerda, você pode adicionar **O WebApplicationFirewallRequestCount** para rastrear o número de solicitações que correspondem às regras do WAF. Filtros personalizados podem ser criados com base em tipos de ação e nomes de regras.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Registros e diagnósticos

O WAF com a Porta da Frente fornece relatórios detalhados sobre cada ameaça detectada. O registro em log é integrado aos Logs de diagnóstico do Azure e os alertas são registrados em um formato json. Esses logs podem ser integrados aos [logs do Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

O FrontdoorAccessLog registra todas as solicitações encaminhadas para back-ends do cliente. FrontdoorWebApplicationFirewallLog registra qualquer solicitação que corresponda a uma regra WAF.

O exemplo a seguir a consulta obtém logs WAF em solicitações bloqueadas:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

Aqui está um exemplo de uma solicitação registrada no registro waf:

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

O exemplo a seguir, a consulta obtém entradas AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Aqui está um exemplo de uma solicitação registrada no log de acesso:

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [a Porta da Frente](../../frontdoor/front-door-overview.md).
