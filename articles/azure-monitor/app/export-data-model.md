---
title: Modelo de dados do Aplicativo Azure insights | Microsoft Docs
description: Descreve as propriedades exportadas de exportação contínua em JSON e usados como filtros.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/08/2019
ms.openlocfilehash: 8f84e3179a6f949e4a322a2218736fc9ebe60442
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677905"
---
# <a name="application-insights-export-data-model"></a>Modelo de dados de exportação Application Insights
Esta tabela lista as propriedades de telemetria enviadas dos SDKs do [Application Insights](../../azure-monitor/app/app-insights-overview.md) para o portal.
Você verá essas propriedades na saída de dados de [Exportação Contínua](export-telemetry.md).
Eles também aparecem em filtros de propriedade no [Gerenciador de métricas](../../azure-monitor/app/metrics-explorer.md) e na [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md).

Pontos a serem observados:

* `[0]` nessas tabelas indica um ponto no caminho no qual você precisa inserir um índice; mas nem sempre é 0.
* As durações de tempo são em décimos de um microssegundo, portanto, 10 milhões = = 1 segundo.
* Datas e horas estão em UTC e são fornecidas no formato ISO `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Exemplo
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

## <a name="context"></a>Noticioso
Todos os tipos de telemetria são acompanhados por uma seção de contexto. Nem todos esses campos são transmitidos com todos os pontos de dados.

| Multi-Path | Tipo | Observações |
| --- | --- | --- |
| Context. Custom. Dimensions [0] |objeto [] |Pares de cadeia de caracteres de chave-valor definidos pelo parâmetro Propriedades personalizadas. Comprimento máximo da chave 100, tamanho máximo de valores 1024. Mais de 100 valores exclusivos, a propriedade pode ser pesquisada, mas não pode ser usada para segmentação. Máximo de 200 chaves por iKey. |
| Context. Custom. métricas [0] |objeto [] |Pares chave-valor definidos pelo parâmetro de medidas personalizadas e por TrackMetrics. Comprimento máximo da chave 100, os valores podem ser numéricos. |
| Context. Data. EventTime |cadeia de caracteres |HORÁRIO |
| Context. Data. issintética |booleano |A solicitação parece vir de um teste na Web ou bot. |
| Context. Data. samplingRate |Automática |Porcentagem de telemetria gerada pelo SDK que é enviado ao Portal. Intervalo 0,0-100.0. |
| contexto. dispositivo |objeto |Dispositivo do cliente |
| contexto. Device. browser |cadeia de caracteres |IE, Chrome,... |
| Context. Device. browserVersion |cadeia de caracteres |Chrome 48,0,... |
| Context. Device. deviceModel |cadeia de caracteres | |
| Context. Device. DeviceName |cadeia de caracteres | |
| context.device.id |cadeia de caracteres | |
| contexto. Device. Locale |cadeia de caracteres |en-GB, de-DE,... |
| contexto. Device. Network |cadeia de caracteres | |
| Context. Device. oemName |cadeia de caracteres | |
| contexto. Device. os |cadeia de caracteres | |
| Context. Device. osVersion |cadeia de caracteres |SO host |
| Context. Device. roleInstance |cadeia de caracteres |ID do host do servidor |
| Context. Device. roleName |cadeia de caracteres | |
| Context. Device. screenResolution |cadeia de caracteres | |
| Context. Device. Type |cadeia de caracteres |PC, navegador,... |
| contexto. local |objeto |Derivado de clientip. |
| Context. Location. City |cadeia de caracteres |Derivado de clientip, se conhecido |
| Context. Location. clientip |cadeia de caracteres |O último octógono é anônimo para 0. |
| Context. Location. continente |cadeia de caracteres | |
| contexto. local. Country |cadeia de caracteres | |
| Context. Location. província |cadeia de caracteres |Estado ou província |
| context.operation.id |cadeia de caracteres |Os itens que têm a mesma ID de operação são mostrados como itens relacionados no Portal. Geralmente a ID da solicitação. |
| context.operation.name |cadeia de caracteres |URL ou nome da solicitação |
| Context. Operation. parentId |cadeia de caracteres |Permite itens relacionados aninhados. |
| context.session.id |cadeia de caracteres |ID de um grupo de operações da mesma origem. Um período de 30 minutos sem uma operação sinaliza o final de uma sessão. |
| Context. Session. isprimeiro |booleano | |
| Context. User. accountAcquisitionDate |cadeia de caracteres | |
| Context. User. AccountId |cadeia de caracteres | |
| Context. User. anonAcquisitionDate |cadeia de caracteres | |
| Context. User. anonid |cadeia de caracteres | |
| Context. User. authAcquisitionDate |cadeia de caracteres |[Usuário autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| Context. User. authid |cadeia de caracteres | |
| Context. User. IsAuthenticated |booleano | |
| Context. User. storeRegion |cadeia de caracteres | |
| Internal. Data. documentVersion |cadeia de caracteres | |
| internal.data.id |cadeia de caracteres | ID exclusiva que é atribuída quando um item é ingerido para Application Insights |

## <a name="events"></a>Eventos
Eventos personalizados gerados por [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Multi-Path | Tipo | Observações |
| --- | --- | --- |
| contagem do evento [0] |valores |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| event [0] name |cadeia de caracteres |Nome do evento.  Comprimento máximo de 250. |
| URL do evento [0] |cadeia de caracteres | |
| evento [0] urlData. base |cadeia de caracteres | |
| evento [0] urlData. host |cadeia de caracteres | |

## <a name="exceptions"></a>Exceções
[Exceções](../../azure-monitor/app/asp-net-exceptions.md) do relatório no servidor e no navegador.

| Multi-Path | Tipo | Observações |
| --- | --- | --- |
| assembly basicexception [0] |cadeia de caracteres | |
| contagem de basicexception [0] |valores |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| camada de exceção basicexception [0] |cadeia de caracteres | |
| ExceptionType de basicexception [0] |cadeia de caracteres | |
| basicexception [0] failedUserCodeMethod |cadeia de caracteres | |
| basicexception [0] failedUserCodeAssembly |cadeia de caracteres | |
| basicexception [0] handledAt |cadeia de caracteres | |
| basicexception [0] hasFullStack |booleano | |
| ID de basicexception [0] |cadeia de caracteres | |
| método basicexception [0] |cadeia de caracteres | |
| basicexception [0] mensagem |cadeia de caracteres |Mensagem de exceção. Comprimento máximo 10K. |
| basicexception [0] outerExceptionMessage |cadeia de caracteres | |
| basicexception [0] outerExceptionThrownAtAssembly |cadeia de caracteres | |
| basicexception [0] outerExceptionThrownAtMethod |cadeia de caracteres | |
| basicexception [0] outerExceptionType |cadeia de caracteres | |
| basicexception [0] externaid |cadeia de caracteres | |
| assembly basicexception [0] parsedStack [0] |cadeia de caracteres | |
| basicexception [0] parsedStack [0] nome do arquivo |cadeia de caracteres | |
| nível basicexception [0] parsedStack [0] |valores | |
| basicexception [0] parsedStack [0] linha |valores | |
| método basicexception [0] parsedStack [0] |cadeia de caracteres | |
| basicexception [0] pilha |cadeia de caracteres |Comprimento máximo 10K |
| typeName de basicexception [0] |cadeia de caracteres | |

## <a name="trace-messages"></a>Mensagens de rastreamento
Enviado por [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)e pelos [adaptadores de registro em log](../../azure-monitor/app/asp-net-trace-logs.md).

| Multi-Path | Tipo | Observações |
| --- | --- | --- |
| mensagem [0] loggername |cadeia de caracteres | |
| parâmetros da mensagem [0] |cadeia de caracteres | |
| mensagem [0] RAW |cadeia de caracteres |A mensagem de log, comprimento máximo 10K. |
| mensagem [0] nível |cadeia de caracteres | |

## <a name="remote-dependency"></a>Dependência remota
Enviado por TrackDependency. Usado para indicar o desempenho e o uso das [chamadas para dependências](../../azure-monitor/app/asp-net-dependencies.md) no servidor, e chamadas do AJAX no navegador.

| Multi-Path | Tipo | Observações |
| --- | --- | --- |
| remoteDependency [0] Async |booleano | |
| remoteDependency [0] baseName |cadeia de caracteres | |
| remoteDependency [0] commandName |cadeia de caracteres |Por exemplo, "home/index" |
| contagem de remoteDependency [0] |valores |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| remoteDependency [0] dependencyTypeName |cadeia de caracteres |HTTP, SQL,... |
| remoteDependency [0] durationMetric. Value |Automática |Tempo desde a chamada até a conclusão da resposta por dependência |
| remoteDependency [0] ID |cadeia de caracteres | |
| remoteDependency [0] nome |cadeia de caracteres |URL. Comprimento máximo de 250. |
| remoteDependency [0] resultCode |cadeia de caracteres |da dependência HTTP |
| remoteDependency [0] êxito |booleano | |
| remoteDependency [0] tipo |cadeia de caracteres |Http, SQL,... |
| URL do remoteDependency [0] |cadeia de caracteres |Comprimento máximo de 2000 |
| remoteDependency [0] urlData. base |cadeia de caracteres |Comprimento máximo de 2000 |
| remoteDependency [0] urlData. hashTag |cadeia de caracteres | |
| remoteDependency [0] urlData. host |cadeia de caracteres |Comprimento máximo de 200 |

## <a name="requests"></a>Solicitações
Enviado por [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Os módulos padrão usam isso para relatar o tempo de resposta do servidor, medido no servidor.

| Multi-Path | Tipo | Observações |
| --- | --- | --- |
| contagem [0] da solicitação |valores |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo: 4 = &gt; 25%. |
| request [0] durationMetric.value |Automática |Tempo da solicitação que chega à resposta. 1e7 = = 1s |
| ID da solicitação [0] |cadeia de caracteres |ID da operação |
| nome da solicitação [0] |cadeia de caracteres |GET/POST + URL base.  Comprimento máximo de 250 |
| solicitar [0] responseCode |valores |Resposta HTTP enviada ao cliente |
| solicitação [0] com êxito |booleano |Padrão = = (responseCode &lt; 400) |
| request [0] url |cadeia de caracteres |Não incluindo host |
| solicitação [0] urlData. base |cadeia de caracteres | |
| solicitação [0] urlData. hashTag |cadeia de caracteres | |
| solicitação [0] urlData. host |cadeia de caracteres | |

## <a name="page-view-performance"></a>Desempenho de exibição de página
Enviado pelo navegador. Mede o tempo para processar uma página, desde o usuário iniciar a solicitação para exibir a conclusão (excluindo chamadas assíncronas AJAX).

Os valores de contexto mostram o so do cliente e a versão do navegador.

| Multi-Path | Tipo | Observações |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |valores |Tempo desde o fim do recebimento do HTML até a exibição da página. |
| clientPerformance [0] nome |cadeia de caracteres | |
| clientPerformance [0] networkConnection. Value |valores |Tempo necessário para estabelecer uma conexão de rede. |
| clientPerformance [0] receiveRequest. Value |valores |Tempo desde o fim do envio da solicitação até o recebimento do HTML em resposta. |
| clientPerformance [0] sendRequest. Value |valores |Tempo desde que foi levado para enviar a solicitação HTTP. |
| clientPerformance [0] total. valor |valores |Tempo desde o início até o envio da solicitação para exibir a página. |
| URL do clientPerformance [0] |cadeia de caracteres |URL desta solicitação |
| clientPerformance [0] urlData. base |cadeia de caracteres | |
| clientPerformance [0] urlData. hashTag |cadeia de caracteres | |
| clientPerformance [0] urlData. host |cadeia de caracteres | |
| clientPerformance [0] urlData. Protocol |cadeia de caracteres | |

## <a name="page-views"></a>Exibições de página
Enviado pelo trackPageView() ou [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Multi-Path | Tipo | Observações |
| --- | --- | --- |
| Exibir [0] contagem |valores |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| Exibir [0] durationMetric. Value |valores |Valor opcionalmente definido em trackPageView () ou por startTrackPage ()-stopTrackPage (). Não é o mesmo que os valores de clientPerformance. |
| Exibir [0] nome |cadeia de caracteres |Título da página.  Comprimento máximo de 250 |
| Exibir [0] URL |cadeia de caracteres | |
| Exibir [0] urlData. base |cadeia de caracteres | |
| Exibir [0] urlData. hashTag |cadeia de caracteres | |
| Exibir [0] urlData. host |cadeia de caracteres | |

## <a name="availability"></a>Disponibilidade
Relata os [testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md).

| Multi-Path | Tipo | Observações |
| --- | --- | --- |
| disponibilidade [0] availabilityMetric.name |cadeia de caracteres |Ininterrupta |
| disponibilidade [0] availabilityMetric. Value |Automática |1,0 ou 0,0 |
| contagem de disponibilidade [0] |valores |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| availability [0] dataSizeMetric.name |cadeia de caracteres | |
| disponibilidade [0] dataSizeMetric. Value |valores | |
| disponibilidade [0] durationMetric.name |cadeia de caracteres | |
| disponibilidade [0] durationMetric. Value |Automática |Duração do teste. 1e7 = = 1s |
| mensagem de disponibilidade [0] |cadeia de caracteres |Diagnóstico de falha |
| resultado da disponibilidade [0] |cadeia de caracteres |Aprovado/reprovado |
| disponibilidade [0] runLocation |cadeia de caracteres |Fonte geográfica de req. http |
| disponibilidade [0] TestName |cadeia de caracteres | |
| disponibilidade [0] testRunId |cadeia de caracteres | |
| disponibilidade [0] testTimestamp |cadeia de caracteres | |

## <a name="metrics"></a>Métricas
Gerado por TrackMetric ().

O valor da métrica é encontrado em Context. Custom. métricas [0]

Por exemplo:

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
         } ] }
    }

## <a name="about-metric-values"></a>Sobre valores de métrica
Os valores de métrica, tanto em relatórios de métricas quanto em outro lugar, são relatados com uma estrutura de objeto padrão. Por exemplo:

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

Atualmente, embora isso possa mudar no futuro, em todos os valores relatados dos módulos padrão do SDK, `count==1` e somente os campos `name` e `value` são úteis. O único caso em que eles poderiam ser diferentes seria se você escrevesse suas próprias chamadas TrackMetric, nas quais definiria os outros parâmetros.

A finalidade dos outros campos é permitir que as métricas sejam agregadas no SDK, para reduzir o tráfego para o Portal. Por exemplo, você pode calcular várias leituras sucessivas antes de enviar cada relatório de métrica. Em seguida, você calcularia o desvio mínimo, máximo, padrão e o valor agregado (Sum ou Average) e definirá Count como o número de leituras representadas pelo relatório.

Nas tabelas acima, omitimos os campos min, max, stdDev e sampledValue, que são usados raramente.

Em vez de agregar previamente as métricas, você pode usar a [amostragem](../../azure-monitor/app/sampling.md) se precisar reduzir o volume de telemetria.

### <a name="durations"></a>Durações
Exceto quando indicado de outra forma, as durações são representadas em décimos de um microssegundo, de modo que 10000000,0 significa 1 segundo.

## <a name="see-also"></a>Consulte também
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Exportação Contínua](export-telemetry.md)
* [Exemplos de código](export-telemetry.md#code-samples)
