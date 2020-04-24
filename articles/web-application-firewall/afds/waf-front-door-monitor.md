---
title: Monitoramento e registro em log do firewall do aplicativo Web do Azure
description: Saiba mais sobre o WAF (firewall do aplicativo Web) com o monitoramento e registro em log do FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: b4f666415a96307b89022c6caf6af90581f294f3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115356"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoramento e registro em log do firewall do aplicativo Web do Azure 

O monitoramento e o registro em log do WAF (firewall do aplicativo Web) do Azure são fornecidos por meio de log e integração com logs de Azure Monitor e Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

O WAF com o log do FrontDoor é integrado ao [Azure monitor](../../azure-monitor/overview.md). Azure Monitor permite que você acompanhe informações de diagnóstico, incluindo alertas e logs do WAF. Você pode configurar o monitoramento de WAF dentro do recurso de porta frontal no portal na guia **diagnóstico** ou por meio do serviço de Azure monitor diretamente.

Em portal do Azure, vá para tipo de recurso da porta frontal. Na guia**métricas** de **monitoramento**/à esquerda, você pode adicionar **WebApplicationFirewallRequestCount** para rastrear o número de solicitações que correspondem às regras de WAF. Os filtros personalizados podem ser criados com base em tipos de ação e nomes de regra.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Logs e diagnósticos

WAF com a porta frontal fornece relatórios detalhados sobre cada ameaça detectada. O registro em log é integrado aos Logs de diagnóstico do Azure e os alertas são registrados em um formato json. Esses logs podem ser integrados aos [logs do Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog registra todas as solicitações que são encaminhadas para back-ends do cliente. FrontdoorWebApplicationFirewallLog registra qualquer solicitação que corresponda a uma regra de WAF.

A consulta de exemplo a seguir obtém logs do WAF em solicitações bloqueadas:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Aqui está um exemplo de uma solicitação registrada no log do WAF:

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

A consulta de exemplo a seguir obtém entradas AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Aqui está um exemplo de uma solicitação registrada no log do Access:

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

- Saiba mais sobre a [porta da frente](../../frontdoor/front-door-overview.md).
