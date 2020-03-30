---
title: Ações do Webhook para alertas de log em alertas do Azure
description: Este artigo descreve como criar uma regra de alerta de log usando o espaço de trabalho do Log Analytics ou o Application Insights, como o alerta empurra os dados como um webhook HTTP e os detalhes das diferentes personalizações possíveis.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667696"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Ações de webhook para regras de alerta do log
Quando um [alerta de log é criado no Azure,](alerts-log.md)você tem a opção de [configurá-lo usando grupos de ação](action-groups.md) para executar uma ou mais ações. Este artigo descreve as diferentes ações de webhook disponíveis e mostra como configurar um webhook personalizado baseado em JSON.

> [!NOTE]
> Você também pode usar o [esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs) para suas integrações de webhook. O esquema de alerta comum oferece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Azure Monitor.Observe que o esquema de alerta comum não honra a opção JSON personalizada para alertas de log. Ele adia para a carga de esquema de alerta comum se isso for selecionado independentemente da personalização que você possa ter feito no nível da regra de alerta. [Conheça as definições comuns do esquema de alerta.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Ações de Webhook

Com ações de webhook, você pode invocar um processo externo através de uma única solicitação HTTP POST. O serviço chamado deve suportar webhooks e determinar como usar qualquer carga que receber.

As ações de webhook exigem as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| **URL de Webhook** |A URL do webhook. |
| **Carga JSON personalizada** |A carga personalizada para enviar com o webhook quando esta opção é escolhida durante a criação de alerta. Para obter mais informações, consulte [Gerenciar alertas de log](alerts-log.md).|

> [!NOTE]
> O botão **Exibir webhook** ao lado da opção **Incluir carga json personalizada para webhook** para o alerta de log exibe a carga útil do webhook de exemplo para a personalização fornecida. Ele não contém dados reais, mas é representativo do esquema JSON que é usado para alertas de log. 

Os webhooks incluem uma URL e uma carga formatado em JSON que os dados enviados para o serviço externo. Por padrão, a carga inclui os valores na tabela a seguir. Você pode optar por substituir essa carga por uma personalizado de sua preferência. Nesse caso, use as variáveis na tabela para cada um dos parâmetros para incluir seus valores em sua carga útil personalizada.


| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| *Nome de regra de alerta* |#alertrulename |Nome da regra de alerta. |
| *Severidade* |#severity |Severidade definida para o alerta do log disparado. |
| *AlertThresholdOperator* |#thresholdoperator |Operador de limiar para a regra de alerta, que usa maior ou menor que. |
| *AlertThresholdValue* |#thresholdvalue |O valor de limite para a regra de alerta. |
| *LinkToSearchResults* |#linktosearchresults |Link para o portal Analytics que retorna os registros da consulta que criou o alerta. |
| *ResultCount* |#searchresultcount |Número de registros nos resultados da pesquisa. |
| *Hora de término do intervalo de pesquisa* |#searchintervalendtimeutc |Tempo de término para a consulta no UTC, com o formato mm/dd/yyyy HH:mm:ss AM/PM. |
| *Intervalo de pesquisa* |#searchinterval |Janela de tempo para a regra de alerta, com o formato HH:mm:ss. |
| *Hora de início do intervalo de pesquisa* |#searchintervalstarttimeutc |Tempo de início da consulta no UTC, com o formato mm/dd/yyyy HH:mm:ss AM/PM. 
| *Searchquery* |#searchquery |A consulta da pesquisa de log usada pela regra de alerta. |
| *SearchResults* |"IncludeSearchResults": true|Os registros retornados pela consulta como uma tabela JSON, limitados aos primeiros 1.000 registros, se "IncludeSearchResults": true é adicionado em uma definição de webhook JSON personalizada como uma propriedade de alto nível. |
| *Tipo de alerta*| #alerttype | O tipo de regra de alerta de log configurada como [medição métrica](alerts-unified-log.md#metric-measurement-alert-rules) ou número [de resultados](alerts-unified-log.md#number-of-results-alert-rules).|
| *Espaço de trabalhoID* |#workspaceid |ID do seu espaço de trabalho do Log Analytics. |
| *ID de aplicação* |#applicationid |ID do seu aplicativo Application Insights. |
| *ID de assinatura* |#subscriptionid |ID da sua assinatura do Azure usado. 

> [!NOTE]
> *LinkToSearchResults* passa parâmetros como *SearchQuery,* *Search Interval StartTime*e *Search Interval End time* in the URL to the Azure portal para visualização na seção Analytics. O portal Azure tem um limite de tamanho URI de aproximadamente 2.000 caracteres. O portal *não* abrirá links fornecidos em alertas se os valores dos parâmetros ultrapassarem o limite. Você pode inserir manualmente detalhes para ver resultados no portal Analytics. Ou, você pode usar a [API REST do Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) ou a [API Log Analytics REST](/rest/api/loganalytics/) para recuperar resultados de forma programática. 

Por exemplo, você pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado *text*. O serviço que este webhook chama espera este parâmetro.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Este exemplo de carga se resolve para algo como o seguinte quando é enviado para o webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Como todas as variáveis em um webhook personalizado devem ser especificadas dentro de um gabinete JSON, como "#searchinterval", o webhook resultante também tem dados variáveis dentro de gabinetes, como "00:05:00".

Para incluir os resultados da pesquisa em uma carga útil personalizada, certifique-se de que **includeSearchResults** seja definido como uma propriedade de alto nível na carga útil JSON. 

## <a name="sample-payloads"></a>Cargas de exemplo
Esta seção mostra cargas de amostra para webhooks para alertas de log. As cargas de exemplo incluem exemplos quando a carga é padrão e quando é personalizada.

### <a name="standard-webhook-for-log-alerts"></a>Webhook padrão para alertas de log 
Ambos os exemplos têm uma carga útil disfarçada com apenas duas colunas e duas linhas.

#### <a name="log-alert-for-log-analytics"></a>Alerta de log para Análise de Log
A carga útil da amostra a seguir é para uma ação padrão de webhook *sem uma opção JSON personalizada* que é usada para alertas baseados no Log Analytics:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> O valor do campo "Gravidade" pode mudar se você [tiver trocado sua preferência de API](alerts-log-api-switch.md) por alertas de log no Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Alerta de log para insights de aplicativos
A carga útil da amostra a seguir é para um webhook padrão *sem uma opção JSON personalizada* quando usada para alertas de log com base em Insights de aplicativos:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alerta de log com carga útil JSON personalizada
Por exemplo, para criar uma carga útil personalizada que inclua apenas o nome de alerta e os resultados da pesquisa, você pode usar o seguinte: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

A carga útil da amostra a seguir é para uma ação de webhook personalizada para qualquer alerta de log:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [alertas de log em alertas do Azure](alerts-unified-log.md).
- Entenda como [gerenciar alertas de log no Azure](alerts-log.md).
- Crie e gerencie [grupos de ação no Azure](action-groups.md).
- Saiba mais sobre [o Application Insights](../../azure-monitor/app/analytics.md).
- Saiba mais sobre [consultas de log](../log-query/log-query-overview.md). 

