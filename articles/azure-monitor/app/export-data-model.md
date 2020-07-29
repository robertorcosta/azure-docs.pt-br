---
title: Modelo de Dados do Azure Application Insights | Microsoft Docs
description: Descreve as propriedades exportadas de exportação contínua em JSON e usados como filtros.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 29ad999c307d1c11e7a584b61d85ed73b9448cb4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324379"
---
# <a name="application-insights-export-data-model"></a>Modelo de dados de exportação do Application Insights
Esta tabela lista as propriedades de telemetria enviadas dos SDKs do [Application Insights](./app-insights-overview.md) para o portal.
Você verá essas propriedades na saída de dados de [Exportação Contínua](export-telemetry.md).
Elas também aparecerão nos filtros da propriedade no [Explorador de Métrica](../platform/metrics-charts.md) e na [Pesquisa de Diagnóstico](./diagnostic-search.md).

Pontos a serem observados:

* `[0]` nessas tabelas indica um ponto no caminho no qual você precisa inserir um índice; mas nem sempre é 0.
* A duração é em décimos de microssegundo, portanto, 10000000 = = 1 segundo.
* Datas e horas estão em UTC e são fornecidas no formato ISO `yyyy-MM-DDThh:mm:ss.sssZ`

## <a name="example"></a>Exemplo

```json
// A server report about an HTTP request
{
  "request": [
    {
      "urlData": { // derived from 'url'
        "host": "contoso.org",
        "base": "/",
        "hashTag": ""
      },
      "responseCode": 200, // Sent to client
      "success": true, // Default == responseCode<400
      // Request id becomes the operation id of child events
      "id": "fCOhCdCnZ9I=",  
      "name": "GET Home/Index",
      "count": 1, // 100% / sampling rate
      "durationMetric": {
        "value": 1046804.0, // 10000000 == 1 second
        // Currently the following fields are redundant:
        "count": 1.0,
        "min": 1046804.0,
        "max": 1046804.0,
        "stdDev": 0.0,
        "sampledValue": 1046804.0
      },
      "url": "/"
    }
  ],
  "internal": {
    "data": {
      "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
      "documentVersion": "1.61"
    }
  },
  "context": {
    "device": { // client browser
      "type": "PC",
      "screenResolution": { },
      "roleInstance": "WFWEB14B.fabrikam.net"
    },
    "application": { },
    "location": { // derived from client ip
      "continent": "North America",
      "country": "United States",
      // last octagon is anonymized to 0 at portal:
      "clientip": "168.62.177.0",
      "province": "",
      "city": ""
    },
    "data": {
      "isSynthetic": true, // we identified source as a bot
      // percentage of generated data sent to portal:
      "samplingRate": 100.0,
      "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
    },
    "user": {
      "isAuthenticated": false,
      "anonId": "us-tx-sn1-azr", // bot agent id
      "anonAcquisitionDate": "0001-01-01T00:00:00Z",
      "authAcquisitionDate": "0001-01-01T00:00:00Z",
      "accountAcquisitionDate": "0001-01-01T00:00:00Z"
    },
    "operation": {
      "id": "fCOhCdCnZ9I=",
      "parentId": "fCOhCdCnZ9I=",
      "name": "GET Home/Index"
    },
    "cloud": { },
    "serverDevice": { },
    "custom": { // set by custom fields of track calls
      "dimensions": [ ],
      "metrics": [ ]
    },
    "session": {
      "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
      "isFirst": true
    }
  }
}
```

## <a name="context"></a>Contexto
Todos os tipos de telemetria são acompanhados por uma seção de contexto. Nem todos esses campos são transmitidos com cada ponto de dados.

| Caminho | Type | Observações |
| --- | --- | --- |
| context.custom.dimensions [0] |object [ ] |Pares de cadeira de caractere chave-valor definidos pelo parâmetro das propriedades personalizadas. Comprimento máximo da chave 100, comprimento máximo dos valores 1024. Mais de 100 valores exclusivos, é possível pesquisar na propriedade, mas não usá-la para segmentação. Máximo de 200 chaves por ikey. |
| context.custom.metrics [0] |object [ ] |Pares de chave-valor definidos pelo parâmetro de medidas personalizadas e por TrackMetrics. Comprimento máximo da chave 100, os valores podem ser numéricos. |
| context.data.eventTime |cadeia de caracteres |UTC |
| context.data.isSynthetic |boolean |A solicitação parece ser proveniente de um teste na Web ou de um bot. |
| context.data.samplingRate |número |Porcentagem de telemetria gerada pelo SDK enviado ao portal. Intervalo 0.0-100.0. |
| context.device |object |Dispositivo de cliente |
| context.device.browser |cadeia de caracteres |IE, Chrome, ... |
| context.device.browserVersion |cadeia de caracteres |Chrome 48.0, ... |
| context.device.deviceModel |cadeia de caracteres | |
| context.device.deviceName |cadeia de caracteres | |
| context.device.id |cadeia de caracteres | |
| context.device.locale |cadeia de caracteres |en-GB, de-DE, ... |
| context.device.network |cadeia de caracteres | |
| context.device.oemName |cadeia de caracteres | |
| context.device.os |cadeia de caracteres | |
| context.device.osVersion |cadeia de caracteres |Sistema operacional do host |
| context.device.roleInstance |cadeia de caracteres |ID do host do servidor |
| context.device.roleName |cadeia de caracteres | |
| context.device.screenResolution |cadeia de caracteres | |
| context.device.type |cadeia de caracteres |PC, navegador,... |
| context.location |object |Derivada de `clientip`. |
| context.location.city |cadeia de caracteres |Derivado de `clientip` , se conhecido |
| context.location.clientip |cadeia de caracteres |Último octógono tornado anônimo pelo valor 0. |
| context.location.continent |cadeia de caracteres | |
| context.location.country |cadeia de caracteres | |
| context.location.province |cadeia de caracteres |Estado ou província |
| context.operation.id |cadeia de caracteres |Os itens que têm o mesmo `operation id` são mostrados como itens relacionados no Portal. Geralmente, o `request id` . |
| context.operation.name |cadeia de caracteres |nome solicitação ou url |
| context.operation.parentId |cadeia de caracteres |Permite itens relacionados aninhados. |
| context.session.id |cadeia de caracteres |`Id`de um grupo de operações da mesma fonte. Um período de 30 minutos sem uma operação sinaliza o término de uma sessão. |
| context.session.isFirst |boolean | |
| context.user.accountAcquisitionDate |cadeia de caracteres | |
| context.user.accountId |cadeia de caracteres | |
| context.user.anonAcquisitionDate |cadeia de caracteres | |
| context.user.anonId |cadeia de caracteres | |
| context.user.authAcquisitionDate |cadeia de caracteres |[Usuário Autenticado](./api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |cadeia de caracteres | |
| context.user.isAuthenticated |boolean | |
| context.user.storeRegion |cadeia de caracteres | |
| internal.data.documentVersion |cadeia de caracteres | |
| internal.data.id |cadeia de caracteres | `Unique id`que é atribuído quando um item é ingerido para Application Insights |

## <a name="events"></a>Eventos
Eventos personalizados gerados por [TrackEvent()](./api-custom-events-metrics.md#trackevent).

| Caminho | Type | Observações |
| --- | --- | --- |
| event [0] count |inteiro |100/(taxa de[amostragem](./sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| event [0] name |cadeia de caracteres |Nome do evento.  Comprimento máximo 250. |
| event [0] url |cadeia de caracteres | |
| event [0] urlData.base |cadeia de caracteres | |
| event [0] urlData.host |cadeia de caracteres | |

## <a name="exceptions"></a>Exceções
[Exceções](./asp-net-exceptions.md) do relatório no servidor e no navegador.

| Caminho | Type | Observações |
| --- | --- | --- |
| basicException [0] assembly |cadeia de caracteres | |
| basicException [0] count |inteiro |100/(taxa de[amostragem](./sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| basicException [0] exceptionGroup |cadeia de caracteres | |
| basicException [0] exceptionType |cadeia de caracteres | |
| basicException [0] failedUserCodeMethod |cadeia de caracteres | |
| basicException [0] failedUserCodeAssembly |cadeia de caracteres | |
| basicException [0] handledAt |cadeia de caracteres | |
| basicException [0] hasFullStack |boolean | |
| basicexception [0]`id` |cadeia de caracteres | |
| basicException [0] method |cadeia de caracteres | |
| basicException [0] message |cadeia de caracteres |Mensagem de exceção. Comprimento máximo 10k. |
| basicException [0] outerExceptionMessage |cadeia de caracteres | |
| basicException [0] outerExceptionThrownAtAssembly |cadeia de caracteres | |
| basicException [0] outerExceptionThrownAtMethod |cadeia de caracteres | |
| basicException [0] outerExceptionType |cadeia de caracteres | |
| basicException [0] outerId |cadeia de caracteres | |
| basicException [0] parsedStack [0] assembly |cadeia de caracteres | |
| basicException [0] parsedStack [0] fileName |cadeia de caracteres | |
| basicException [0] parsedStack [0] level |inteiro | |
| basicException [0] parsedStack [0] line |inteiro | |
| basicException [0] parsedStack [0] method |cadeia de caracteres | |
| basicException [0] stack |cadeia de caracteres |Comprimento máximo 10k |
| basicException [0] typeName |cadeia de caracteres | |

## <a name="trace-messages"></a>Mensagens de rastreamento
Enviado por [TrackTrace](./api-custom-events-metrics.md#tracktrace) e pelos [adaptadores de log](./asp-net-trace-logs.md).

| Caminho | Type | Observações |
| --- | --- | --- |
| message [0] loggerName |cadeia de caracteres | |
| message [0] parameters |cadeia de caracteres | |
| message [0] raw |cadeia de caracteres |A mensagem de log, comprimento máximo de 10 mil. |
| message [0] severityLevel |cadeia de caracteres | |

## <a name="remote-dependency"></a>Dependência remota
Enviado por TrackDependency. Usado para indicar o desempenho e o uso das [chamadas para dependências](./asp-net-dependencies.md) no servidor, e chamadas do AJAX no navegador.

| Caminho | Type | Observações |
| --- | --- | --- |
| remoteDependency [0] async |boolean | |
| remoteDependency [0] baseName |cadeia de caracteres | |
| remoteDependency [0] commandName |cadeia de caracteres |Por exemplo, "home/index" |
| remoteDependency [0] count |inteiro |100/(taxa de[amostragem](./sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |cadeia de caracteres |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |número |Tempo desde a chamada até a conclusão da resposta por dependência |
| remoteDependency [0]`id` |cadeia de caracteres | |
| remoteDependency [0] name |cadeia de caracteres |Url. Comprimento máximo 250. |
| remoteDependency [0] resultCode |cadeia de caracteres |da dependência de HTTP |
| remoteDependency [0] success |boolean | |
| remoteDependency [0] type |cadeia de caracteres |Http, Sql,... |
| remoteDependency [0] url |cadeia de caracteres |Comprimento máximo 2000 |
| remoteDependency [0] urlData.base |cadeia de caracteres |Comprimento máximo 2000 |
| remoteDependency [0] urlData.hashTag |cadeia de caracteres | |
| remoteDependency [0] urlData.host |cadeia de caracteres |Comprimento máximo 200 |

## <a name="requests"></a>Requests
Enviado por [TrackRequest](./api-custom-events-metrics.md#trackrequest). Os módulos padrão usam isso para indicar o tempo de resposta do servidor, medido no servidor.

| Caminho | Type | Observações |
| --- | --- | --- |
| request [0] count |inteiro |100/(taxa de[amostragem](./sampling.md) ). Por exemplo: 4 =&gt; 25%. |
| request [0] durationMetric.value |número |Tempo de chegada da solicitação até a resposta. 1e7 == 1s |
| solicitação [0]`id` |cadeia de caracteres |`Operation id` |
| request [0] name |cadeia de caracteres |GET/POST + url base.  Comprimento máximo 250 |
| request [0] responseCode |inteiro |Resposta HTTP enviada ao cliente |
| request [0] success |boolean |Padrão == (responseCode &lt; 400) |
| request [0] url |cadeia de caracteres |Não incluindo o host |
| request [0] urlData.base |cadeia de caracteres | |
| request [0] urlData.hashTag |cadeia de caracteres | |
| request [0] urlData.host |cadeia de caracteres | |

## <a name="page-view-performance"></a>Desempenho de exibição da página
Enviado pelo navegador. Mede o tempo de processamento de uma página, desde o início da solicitação do usuário até a exibição completa (excluindo as chamadas do AJAX assíncronas).

Os valores de contexto mostram a versão do navegador e do sistema operacional cliente.

| Caminho | Type | Observações |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |inteiro |Tempo desde o término do recebimento do HTML até a exibição da página. |
| clientPerformance [0] name |cadeia de caracteres | |
| clientPerformance [0] networkConnection.value |inteiro |Tempo necessário para estabelecer uma conexão de rede. |
| clientPerformance [0] receiveRequest.value |inteiro |Tempo desde o término do envio da solicitação até o recebimento do HTML na resposta. |
| clientPerformance [0] sendRequest.value |inteiro |Tempo necessário para enviar a solicitação HTTP. |
| clientPerformance [0] total.value |inteiro |Tempo desde o início até o envio da solicitação para exibição da página. |
| clientPerformance [0] url |cadeia de caracteres |URL dessa solicitação |
| clientPerformance [0] urlData.base |cadeia de caracteres | |
| clientPerformance [0] urlData.hashTag |cadeia de caracteres | |
| clientPerformance [0] urlData.host |cadeia de caracteres | |
| clientPerformance [0] urlData.protocol |cadeia de caracteres | |

## <a name="page-views"></a>Visualizações de página
Enviado por trackPageView() ou [stopTrackPage](./api-custom-events-metrics.md#page-views)

| Caminho | Type | Observações |
| --- | --- | --- |
| view [0] count |inteiro |100/(taxa de[amostragem](./sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| view [0] durationMetric.value |inteiro |Valor definido opcionalmente em trackPageView() ou por startTrackPage() - stopTrackPage(). Não é igual aos valores de clientPerformance. |
| view [0] name |cadeia de caracteres |Título da página.  Comprimento máximo 250 |
| view [0] url |cadeia de caracteres | |
| view [0] urlData.base |cadeia de caracteres | |
| view [0] urlData.hashTag |cadeia de caracteres | |
| view [0] urlData.host |cadeia de caracteres | |

## <a name="availability"></a>Disponibilidade
Relata os [testes de disponibilidade na Web](./monitor-web-app-availability.md).

| Caminho | Type | Observações |
| --- | --- | --- |
| availability [0] availabilityMetric.name |cadeia de caracteres |availability |
| availability [0] availabilityMetric.value |número |1.0 ou 0.0 |
| availability [0] count |inteiro |100/(taxa de[amostragem](./sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| availability [0] dataSizeMetric.name |cadeia de caracteres | |
| availability [0] dataSizeMetric.value |inteiro | |
| availability [0] durationMetric.name |cadeia de caracteres | |
| availability [0] durationMetric.value |número |Duração do teste. 1e7==1s |
| availability [0] message |cadeia de caracteres |Diagnóstico de falha |
| availability [0] result |cadeia de caracteres |Aprovado/Reprovado |
| availability [0] runLocation |cadeia de caracteres |Fonte geográfica de solicitações http |
| availability [0] testName |cadeia de caracteres | |
| availability [0] testRunId |cadeia de caracteres | |
| availability [0] testTimestamp |cadeia de caracteres | |

## <a name="metrics"></a>Métricas
Gerado por TrackMetric().

O valor da métrica é encontrado em context.custom.metrics[0]

Por exemplo:

```json
{
  "metric": [ ],
  "context": {
  ...
    "custom": {
      "dimensions": [
        { "ProcessId": "4068" }
      ],
      "metrics": [
        {
          "dispatchRate": {
            "value": 0.001295,
            "count": 1.0,
            "min": 0.001295,
            "max": 0.001295,
            "stdDev": 0.0,
            "sampledValue": 0.001295,
            "sum": 0.001295
          }
        }
      ]  
    }
  }
}
```

## <a name="about-metric-values"></a>Sobre valores de métricas
Valores de métricas, tanto em relatórios de métrica quanto em outros locais, são relatados com uma estrutura de objeto padrão. Por exemplo:

```json
"durationMetric": {
  "name": "contoso.org",
  "type": "Aggregation",
  "value": 468.71603053650279,
  "count": 1.0,
  "min": 468.71603053650279,
  "max": 468.71603053650279,
  "stdDev": 0.0,
  "sampledValue": 468.71603053650279
}
```

Atualmente, embora isso possa mudar no futuro, em todos os valores relatados pelos módulos padrão do SDK, `count==1` e apenas os campos `name` e `value` são úteis. O único caso em que eles poderiam ser diferentes seria se você escrevesse suas próprias chamadas TrackMetric, nas quais definiria os outros parâmetros.

A finalidade dos outros campos é permitir que as métricas sejam agregadas ao SDK, a fim de reduzir o tráfego no portal. Por exemplo, você pode realizar várias leituras sucessivas antes de enviar cada relatório de métricas. Depois, você deve calcular o desvio mín., máx. e padrão e o valor agregado (soma ou média), e definir a contagem como o número de leituras representado pelo relatório.

Nas tabelas acima, omitimos o número de campos raramente usados, min, Max, stdDev e sampledValue.

Em vez de agregar previamente as métricas, você pode usar a [amostragem](./sampling.md) se precisar reduzir o volume de telemetria.

### <a name="durations"></a>Durações
Exceto quando indicado o contrário, as durações são representadas em décimos de microssegundo, de modo que 10000000.0 significa 1 segundo.

## <a name="see-also"></a>Confira também
* [Application Insights](./app-insights-overview.md)
* [Exportação contínua](export-telemetry.md)
* [Exemplos de código](export-telemetry.md#code-samples)

