---
title: Transmitir logs de plataforma do Azure para um hub de eventos
description: Aprenda a transmitir logs de recursos do Azure para um hub de eventos para enviar dados para sistemas externos, como SIEMs de terceiros e outras soluções de análise de log.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658907"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>A plataforma Stream Azure faz logs no Azure Event Hubs
[Os logs de plataforma](platform-logs-overview.md) no Azure, incluindo logs de atividades do Azure e registros de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure da qual dependem.  Este artigo descreve os logs de plataformas de streaming para hubs de eventos para enviar dados para sistemas externos, como SIEMs de terceiros e outras soluções de análise de log.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>O que você pode fazer com logs de plataforma enviados para um hub de eventos
A plataforma de streaming faz login no Azure para hubs de eventos para fornecer as seguintes funcionalidades:

* **Stream logs para sistemas de registro e telemetria** de terceiros – Transmita todos os logs de sua plataforma para um único hub de eventos para canalizar dados de log para uma ferramenta de análise de log de terceiros.
  
* **Construa uma plataforma personalizada de telemetria e registro** – A natureza altamente escalável de assinatura de publicações dos hubs de eventos permite que você ingerisse de forma flexível os logs de plataforma em uma plataforma de teletria personalizada. Consulte [Projetando e Dimensionando uma plataforma de telemetria em escala global no Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) para obter detalhes.

* **Veja a saúde do serviço transmitindo dados para o Power BI** – Use Hubs de Eventos, Stream Analytics e Power BI para transformar seus dados de diagnóstico em insights quase em tempo real sobre seus serviços do Azure. Consulte [Stream Analytics e Power BI: Um painel de análise em tempo real para transmitir dados](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para obter detalhes sobre esta solução.

    O código SQL a seguir é um exemplo de consulta do Stream Analytics que você pode usar para analisar todos os dados de log em uma tabela do Power BI:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Pré-requisitos
Você precisa [criar um hub de eventos](../../event-hubs/event-hubs-create.md) se você ainda não tiver um. Se você já tiver uma configuração de diagnóstico usando este namespace do Event Hubs, então esse hub de eventos será reutilizado.

A política de acesso compartilhado para o namespace define as permissões que o mecanismo de streaming possui. O streaming para hubs de eventos requer permissões de gerenciamento, envio e escuta. Você pode criar ou modificar políticas de acesso compartilhado no portal Azure na guia Configurar para o namespace do seu Event Hubs.

Para atualizar a configuração de diagnóstico para incluir o streaming, você deve ter a permissão ListKey nessa regra de autorização do Event Hubs. O namespace dos Hubs de Eventos não precisa estar na mesma assinatura que emite os logs, desde que o usuário que define a configuração tenha acesso RBAC adequado a ambas as assinaturas e as duas assinaturas estejam no mesmo locatário do ADD.

## <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico
Envie logs de plataforma para um hub de eventos e outros destinos, criando uma configuração de diagnóstico para um recurso do Azure. Consulte [Criar configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md) para obter detalhes.

## <a name="collect-data-from-compute-resources"></a>Coletar dados de recursos computacionais
As configurações de diagnóstico coletarão registros de recursos para os recursos de computação do Azure como qualquer outro recurso, mas não seu sistema operacional convidado ou cargas de trabalho. Para coletar esses dados, instale o [agente Log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Consumindo dados de log de hubs de eventos
Os logs de plataforma dos hubs de eventos são consumidos no formato JSON com os elementos na tabela a seguir.

| Nome do elemento | Descrição |
| --- | --- |
| records |Uma matriz de todos os eventos de log nessa carga. |
| time |A hora na qual o evento ocorreu. |
| category |Categoria do log desse evento. |
| resourceId |ID de recurso do recurso que gerou esse evento. |
| operationName |Nome da operação. |
| level |Opcional. Indica o nível do evento de log. |
| properties |Propriedades do evento. Elas irão variar para cada serviço do Azure, conforme descrito em [](). |


A seguir estão os dados de saída de exemplo do Event Hubs para um registro de recursos:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Próximas etapas

* [Leia mais sobre registros de recursos.](platform-logs-overview.md)
* [Crie configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md).
* [Stream Azure Active Directory logs com o Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Comece com hubs de eventos.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

