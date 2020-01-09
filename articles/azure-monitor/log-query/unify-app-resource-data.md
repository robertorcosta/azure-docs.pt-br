---
title: Unificar vários recursos do Application Insights do Azure Monitor | Microsoft Docs
description: Este artigo fornece detalhes sobre como usar uma função nos Logs do Azure Monitor para consultar vários recursos do Application Insights e visualizar esses dados.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 07dd4c96ba51b1ac1e0cb2807c9e26df87a6daa7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364961"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unificar vários recursos do Application Insights do Azure Monitor 
Este artigo descreve como consultar e exibir todos os seus Application Insights dados de log em um único local, mesmo quando eles estão em assinaturas diferentes do Azure, como uma substituição para a reprovação da Conector do Application Insights. O número de recursos de Application Insights que você pode incluir em uma única consulta é limitado a 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Abordagem recomendada para consultar vários recursos do Application Insights 
Listar vários recursos do Application Insights em uma consulta pode ser complicado e difícil de manter. Em vez disso, você pode aproveitar a função para separar a lógica da consulta do escopo dos aplicativos.  

Este exemplo demonstra como você pode monitorar vários recursos do Application Insights e visualizar a contagem de solicitações com falha pelo nome do aplicativo. Antes de começar, execute esta consulta no workspace conectado aos recursos do Application Insights para obter a lista de aplicativos conectados: 

```
ApplicationInsights
| summarize by ApplicationName
```

Crie uma função usando o operador de união com a lista de aplicativos e, em seguida, salve a consulta em seu workspace como uma função com o alias *applicationsScoping*. 

Você pode modificar os aplicativos listados a qualquer momento navegando até o Gerenciador de consultas no seu workspace e selecionando a função para editar e salvar, ou então, usando o cmdlet `SavedSearch` do PowerShell. 

>[!NOTE]
>Esse método não pode ser usado com alertas de log porque a validação de acesso dos recursos de regra de alerta, incluindo espaços de trabalho e aplicativos, é executada no momento da criação do alerta. Não há suporte para a adição de novos recursos à função após a criação do alerta. Se preferir usar a função para o escopo de recursos em alertas de log, você precisará editar a regra de alerta no portal ou com um modelo do Resource Manager para atualizar os recursos com escopo. Como alternativa, você pode incluir a lista de recursos na consulta de alerta de log.

O comando `withsource= SourceApp` adiciona aos resultados uma coluna que designa o aplicativo que enviou o log. O operador Parse é opcional neste exemplo e usa para extrair o nome do aplicativo da propriedade SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Agora você pode usar a função applicationsScoping na consulta entre recursos:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A consulta usa o esquema do Application Insights, embora ela seja executada no workspace, pois a função applicationsScoping retorna a estrutura de dados do Application Insights. O alias de função retorna a união das solicitações de todos os aplicativos definidos. A consulta então filtra solicitações com falha e visualiza as tendências por aplicativo.

![Exemplo de resultados de consulta cruzada](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Consulta entre dados do workspace e recursos do Application Insights 
Quando você interrompe o Conector e precisa executar consultas em um intervalo de tempo que foi reduzido pela retenção de dados do Application Insights (90 dias), você precisa realizar [consultas de recursos cruzados](../../azure-monitor/log-query/cross-workspace-query.md) no workspace e nos recursos do Application Insights por um período intermediário. Isso é válido até que os dados de seus aplicativos se acumulem devido à nova retenção de dados do Application Insights mencionada acima. A consulta requer algumas manipulações, pois os esquemas no Application Insights e no workspace são diferentes. Consulte a tabela nesta seção que destaca as diferenças de esquema. 

>[!NOTE]
>A [consulta de recursos cruzados](../log-query/cross-workspace-query.md) nos alertas de log é compatível com a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por padrão, o Azure Monitor usa a [API herdada de alertas do Log Analytics](../platform/api-alerts.md) para a criação de novas regras de alertas de log do portal do Azure, mas você pode mudar para a [API herdada de alertas de log](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a mudança, a nova API torna-se o padrão para novas regras de alerta no portal do Azure e permite criar regras de alertas de log de consulta de recursos cruzados. Você pode criar regras de alerta de log de [consulta de recursos cruzados](../log-query/cross-workspace-query.md) sem fazer a mudança com o [modelo do ARM para a API scheduledQueryRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template), mas essa regra de alerta pode ser gerenciada na [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não no portal do Azure.

Por exemplo, se o conector parou de funcionar em 2018-11-01, quando você consultar logs em dados de aplicativos e recursos do Application Insights no workspace, sua consulta será construída como o exemplo a seguir:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Diferenças de esquema de espaço de trabalho do Application Insights e do Log Analytics
A tabela a seguir mostra as diferenças de esquema entre o Log Analytics e o Application Insights.  

| Propriedades do espaço de trabalho do Log Analytics| Propriedades de recurso do Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | local |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Navegador | client_browser |
| Cidade | client_city |
| ClientIP | client_IP |
| Computador | cloud_RoleInstance | 
| País | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| SISTEMA OPERACIONAL | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | sucesso | 
| ResponseCode | resultCode | 
| Função | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Próximos passos

Use a [Pesquisa de Logs](../../azure-monitor/log-query/log-query-overview.md) para exibir informações detalhadas dos aplicativos do Application Insights.
