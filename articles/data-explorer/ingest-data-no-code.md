---
title: 'Tutorial: Ingerir dados de monitoramento no Azure Data Explorer sem código'
description: Neste tutorial, você aprenderá a ingerir dados de monitoramento no Azure Data Explorer sem uma linha de código e a consultar esses dados.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 3a53a660da2257540f23bc6438fc5933e5229c76
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78198041"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Tutorial: Ingerir e consultar dados de monitoramento no Azure Data Explorer 

Este tutorial ensinará você a ingerir dados dos logs de diagnóstico e de atividades em um cluster do Azure Data Explorer sem nenhuma codificação. Com esse método de ingestão simples, você poderá começar a consultar o Azure Data Explorer rapidamente para análise de dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie tabelas e mapeamento de ingestão em um banco de dados do Azure Data Explorer.
> * Formate os dados ingeridos usando uma política de atualização.
> * Crie um [hub de eventos](/azure/event-hubs/event-hubs-about) e conecte-o ao Azure Data Explorer.
> * Transmita dados para um hub de eventos dos [logs e métricas de diagnóstico](/azure/azure-monitor/platform/diagnostic-settings) do Azure Monitor e dos [logs de atividades](/azure/azure-monitor/platform/activity-logs-overview).
> * Consulte os dados ingeridos usando o Azure Data Explorer.

> [!NOTE]
> Crie todos os recursos na mesma localização ou região do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster e um banco de dados do Azure Data Explorer](create-cluster-database-portal.md). Neste tutorial, o nome do banco de dados é *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Provedor de dados do Azure Monitor: métricas e logs de diagnóstico e logs de atividades

Exiba e compreenda os dados fornecidos pelos logs e métricas de diagnóstico e logs de atividades do Azure Monitor abaixo. Você criará um pipeline de ingestão com base nesses esquemas de dados. Observe que cada evento em um log tem uma matriz de registros. Essa matriz de registros será dividida adiante no tutorial.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Exemplos de métricas e logs de diagnóstico e logs de atividades

As métricas e os logs de diagnóstico e de atividades do Azure são emitidos por um serviço do Azure e fornecem dados sobre a operação desse serviço. 

# <a name="diagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
#### <a name="example"></a>Exemplo

As métricas de diagnóstico são agregadas com um intervalo de agregação de um minuto. A seguir há um exemplo de um esquema de evento de métrica do Azure Data Explorer sobre a duração da consulta:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[Logs de diagnóstico](#tab/diagnostic-logs)
#### <a name="example"></a>Exemplo

A seguir há um exemplo de um [log de ingestão de diagnóstico](using-diagnostic-logs.md#diagnostic-logs-schema) do Azure Data Explorer:

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[Logs de atividade](#tab/activity-logs)
#### <a name="example"></a>Exemplo

Os logs de atividades do Azure são logs de nível de assinatura que fornecem insight sobre as operações executadas nos recursos em sua assinatura. A seguir, há um exemplo de um evento de log de atividades para verificar o acesso:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Configurar um pipeline de ingestão no Azure Data Explorer

A configuração de um pipeline do Azure Data Explorer envolve várias etapas, como [criação de tabela e ingestão de dados](/azure/data-explorer/ingest-sample-data#ingest-data). Também é possível manipular, mapear e atualizar os dados.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Conectar-se à IU da Web do Azure Data Explorer

No banco de dados *TestDatabase* do Azure Data Explorer, selecione **Consulta** para abrir a IU da Web do Azure Data Explorer.

![Página Consulta](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Criar as tabelas de destino

A estrutura dos logs do Azure Monitor não é tabular. Você manipulará os dados e expandirá cada evento para um ou mais registros. Os dados brutos serão ingeridos a uma tabela intermediária denominada *ActivityLogsRawRecords* para os logs de atividade e *DiagnosticRawRecords* para métricas e logs de diagnóstico. Nesse momento, os dados serão manipulados e expandidos. Usando uma política de atualização, os dados expandidos serão, em seguida, ingeridos na tabela *ActivityLogs* para logs de atividades, *DiagnosticMetrics* para métricas de diagnóstico e *DiagnosticLogs* para logs de diagnóstico. Isso significa que você precisará criar duas tabelas separadas para ingerir os logs de atividades e três tabelas separadas para ingerir as métricas e os logs de diagnóstico.

Use a IU da Web do Azure Data Explorer para criar as tabelas de destino no banco de dados do Azure Data Explorer.

# <a name="diagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Criar tabelas para as métricas de diagnóstico

1. No banco de dados *TestDatabase*, crie uma tabela chamada *DiagnosticMetrics* para armazenar os registros das métricas de diagnóstico. Use o seguinte comando de controle `.create table`:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selecione **Executar** para criar a tabela.

    ![Executar consulta](media/ingest-data-no-code/run-query.png)

1. Crie a tabela de dados intermediários denominada *DiagnosticRawRecords* no banco de dados *TestDatabase* para manipulação de dados usando a consulta a seguir. Selecione **Executar** para criar a tabela.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Defina [política de retenção](/azure/kusto/management/retention-policy) zero para a tabela intermediária:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[Logs de diagnóstico](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Criar tabelas para os logs de diagnóstico 

1. No banco de dados *TestDatabase*, crie uma tabela chamada *DiagnosticLogs* para armazenar os registros do log de diagnóstico. Use o seguinte comando de controle `.create table`:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Selecione **Executar** para criar a tabela.

1. Crie a tabela de dados intermediários denominada *DiagnosticRawRecords* no banco de dados *TestDatabase* para manipulação de dados usando a consulta a seguir. Selecione **Executar** para criar a tabela.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Defina [política de retenção](/azure/kusto/management/retention-policy) zero para a tabela intermediária:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[Logs de atividade](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Criar tabelas para os logs de atividades 

1. Crie uma tabela chamada *ActivityLogs* no banco de dados *TestDatabase* para receber os registros do log de atividades. Para criar a tabela, execute a seguinte consulta do Azure Data Explorer:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Crie a tabela de dados intermediária chamada *ActivityLogsRawRecords* no banco de dados *TestDatabase* para manipulação de dados:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Defina [política de retenção](/azure/kusto/management/retention-policy) zero para a tabela intermediária:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Criar mapeamentos de tabela

 Como o formato de dados é `json`, o mapeamento de dados é necessário. O mapeamento `json` mapeia cada caminho JSON para um nome de coluna de tabela.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Métricas de diagnóstico/logs de diagnóstico](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Mapear métricas e logs de diagnóstico para a tabela

Para mapear os dados das métricas e dos logs de diagnóstico para a tabela, use a seguinte consulta:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[Logs de atividade](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Mapear logs de atividade para a tabela

Para mapear os dados do log de atividades para a tabela, use a seguinte consulta:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Criar a política de atualização para os dados das métricas e dos logs

# <a name="diagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Criar política de atualização de dados para métricas de diagnóstico

1. Crie uma [função](/azure/kusto/management/functions) que expande a coleção dos registros da métrica de diagnóstico de modo que cada valor na coleção receba uma linha separada. Use o operador [`mv-expand`](/azure/kusto/query/mvexpandoperator):
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Adicione a [política de atualização](/azure/kusto/concepts/updatepolicy) à tabela de destino. Essa política executará a consulta automaticamente nos dados recém-ingeridos na tabela de dados intermediária *DiagnosticRawRecords* e ingerirá os resultados na tabela *DiagnosticMetrics*:

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[Logs de diagnóstico](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Criar política de atualização de dados para logs de diagnóstico

1. Crie uma [função](/azure/kusto/management/functions) que expande a coleção dos registros dos logs de diagnóstico de modo que cada valor na coleção receba uma linha separada. Você habilitará os logs de ingestão em um cluster do Azure Data Explorer e usará o [esquema de logs de ingestão](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema). Você criará uma tabela para ingestão com êxito e com falha, enquanto alguns dos campos estarão vazios para a ingestão com êxito (ErrorCode, por exemplo). Use o operador [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Adicione a [política de atualização](/azure/kusto/concepts/updatepolicy) à tabela de destino. Essa política executará a consulta automaticamente nos dados recém-ingeridos na tabela de dados intermediária *DiagnosticRawRecords* e ingerirá os resultados na tabela *DiagnosticLogs*:

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[Logs de atividade](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Criar política de atualização de dados dos logs de atividades

1. Crie uma [função](/azure/kusto/management/functions) que expande a coleção de registros de log de atividades de forma que cada valor na coleção receba uma linha separada. Use o operador [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Adicione a [política de atualização](/azure/kusto/concepts/updatepolicy) à tabela de destino. Essa política executará a consulta automaticamente nos dados recém-ingeridos na tabela de dados intermediária *ActivityLogsRawRecords* e ingerirá os resultados na tabela *ActivityLogs*:

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos do Azure

As configurações de diagnóstico do Azure permitem a exportação de métricas e logs para uma conta de armazenamento ou um hub de eventos. Neste tutorial, rotearemos as métricas e os logs por meio de um hub de eventos. Você criará um namespace dos Hubs de Eventos e um hub de eventos para as métricas e os logs de diagnóstico nas etapas a seguir. O Azure Monitor criará o hub de eventos *insights-operational-logs* para os logs de atividades.

1. Crie um hub de eventos usando um modelo do Azure Resource Manager no portal do Azure. Para seguir o restante das etapas deste artigo, clique com o botão direito do mouse no botão **Implantar no Azure** e, em seguida, selecione **Abrir em uma nova janela**. O botão **Implantar no Azure** leva você ao portal do Azure.

    [![Botão Implantar no Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Crie um namespace dos Hubs de Eventos e um hub de eventos para os logs de diagnóstico.

    ![Criação do hub de eventos](media/ingest-data-no-code/event-hub.png)

1. Preencha o formulário com as seguintes informações. Para as configurações não listadas na tabela a seguir, use os valores padrão.

    **Configuração** | **Valor sugerido** | **Descrição**
    |---|---|---|
    | **Assinatura** | *Sua assinatura* | Selecione a assinatura do Azure que você deseja usar para o seu hub de eventos.|
    | **Grupo de recursos** | *test-resource-group* | Crie um novo grupo de recursos. |
    | **Localidade** | Selecione a região que melhor atende às suas necessidades. | Crie o namespace dos Hubs de Eventos na mesma localização dos outros recursos.
    | **Nome do namespace** | *AzureMonitoringData* | Escolha um nome exclusivo que identifique seu namespace.
    | **Nome do Hub de Eventos** | *DiagnosticData* | O hub de eventos fica sob o namespace, que fornece um contêiner de determinação de escopo exclusivo. |
    | **Nome do grupo de consumidor** | *adxpipeline* | Crie um nome de grupo de consumidores. Grupos de consumidores permitem que vários aplicativos de consumo tenham uma visão separada do fluxo de eventos. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Conectar as métricas e os logs do Azure Monitor ao hub de eventos

Agora, você precisa conectar as métricas e os logs de diagnóstico e os logs de atividades ao hub de eventos.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Métricas de diagnóstico/logs de diagnóstico](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Conectar as métricas e os logs de diagnóstico ao hub de eventos

Selecione um recurso do qual exportar métricas. Vários tipos de recurso dão suporte à exportação dos dados de diagnóstico, incluindo o namespace dos Hubs de Eventos, o Azure Key Vault, o Hub IoT do Azure e clusters do Azure Data Explorer. Neste tutorial, usaremos um cluster do Azure Data Explorer como nosso recurso, examinaremos as métricas de desempenho de consulta e os logs de resultados de ingestão.

1. Selecione o cluster Kusto no portal do Azure.
1. Selecione **Configurações de diagnóstico** e, em seguida, selecione o link **Ativar diagnóstico**. 

    ![Configurações de Diagnóstico](media/ingest-data-no-code/diagnostic-settings.png)

1. O painel **Configurações de diagnóstico** será aberto. Siga estas etapas:
   1. Dê aos dados de log de diagnóstico o nome *ADXExportedData*.
   1. Em **LOG**, marque as caixas de seleção **SucceededIngestion** e **FailedIngestion**.
   1. Em **MÉTRICA**, marque a caixa de seleção **Desempenho de consulta**.
   1. Marque a caixa de seleção **Transmitir para um hub de eventos**.
   1. Selecione **Configurar**.

      ![Painel Configurações de diagnóstico](media/ingest-data-no-code/diagnostic-settings-window.png)

1. No painel **Selecionar um hub de eventos**, configure como exportar os dados dos logs de diagnóstico para o hub de eventos criado:
    1. Na lista **Selecionar um namespace do hub de eventos**, selecione *AzureMonitoringData*.
    1. Na lista **Selecionar o nome do hub de evento**, selecione *DiagnosticData*.
    1. Na lista **Selecionar o nome da política do hub de eventos**, selecione **RootManagerSharedAccessKey**.
    1. Selecione **OK**.

1. Clique em **Salvar**.

# <a name="activity-logs"></a>[Logs de atividade](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Conectar os logs de atividades ao hub de eventos

1. No menu à esquerda do portal do Azure, selecione **Log de atividades**.
1. A janela **Log de atividades** será aberta. Selecione **Exportar para o Hub de Eventos**.

    ![Janela Log de atividades](media/ingest-data-no-code/activity-log.png)

1. A janela **Exportar log de atividades** será aberta:
 
    ![Janela Exportar log de atividades](media/ingest-data-no-code/export-activity-log.png)

1. Na janela **Exportar log de atividades**, execute as seguintes etapas:
      1. Selecione sua assinatura.
      1. Na lista **Regiões**, escolha **Selecionar tudo**.
      1. Marque a caixa de seleção **Exportar para um hub de eventos**.
      1. Escolha **Selecionar um namespace de barramento de serviço** para abrir o painel **Selecionar um hub de eventos**.
      1. No painel **Selecionar um hub de eventos**, selecione sua assinatura.
      1. Na lista **Selecionar um namespace do hub de eventos**, selecione *AzureMonitoringData*.
      1. Na lista **Selecionar o nome da política do hub de eventos**, selecione o nome da política padrão do hub de eventos.
      1. Selecione **OK**.
      1. No canto superior esquerdo da janela, selecione **Salvar**.
   Será criado um hub de eventos com o nome *insights-operational-logs*.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Ver os dados fluindo para os hubs de eventos

1. Aguarde alguns minutos até a conexão ser definida e a exportação do log de atividades para o hub de eventos ser concluída. Acesse o namespace dos Hubs de Eventos para ver os hubs de eventos criados.

    ![Hubs de eventos criados](media/ingest-data-no-code/event-hubs-created.png)

1. Veja os dados fluindo para o hub de eventos:

    ![Dados do hub de eventos](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Conectar um hub de eventos ao Azure Data Explorer

Agora, você precisa criar as conexões de dados para as métricas e os logs de diagnóstico e os logs de atividades.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Criar as conexões de dados para as métricas e os logs de diagnóstico e os logs de atividades

1. No cluster do Azure Data Explorer chamado *kustodocs*, selecione **Bancos de Dados** no menu à esquerda.
1. Na janela **Bancos de Dados**, selecione o banco de dados *TestDatabase*.
1. No menu à esquerda, selecione **Ingestão de dados**.
1. Na janela **Ingestão de dados**, clique em **+ Adicionar Conexão de Dados**.
1. Na janela **Conexão de dados**, insira as seguintes informações:

    ![Conexão de dados do hub de eventos](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Métricas de diagnóstico/logs de diagnóstico](#tab/diagnostic-metrics+diagnostic-logs) 

1. Use as seguintes configurações na janela **Conexão de Dados**:

    Fonte de dados:

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da conexão de dados** | *DiagnosticsLogsConnection* | O nome da conexão que você deseja criar no Azure Data Explorer.|
    | **Namespace do hub de eventos** | *AzureMonitoringData* | O nome escolhido anteriormente que identifica seu namespace. |
    | **Hub de eventos** | *DiagnosticData* | O hub de eventos que você criou. |
    | **Grupo de consumidores** | *adxpipeline* | O grupo de consumidores definido no hub de eventos que você criou. |
    | | |

    Tabela de destino:

    Há duas opções de roteamento: *estático* e *dinâmico*. Para este tutorial, você usará o roteamento estático (o padrão), no qual especificará o nome da tabela, o formato de dados e o mapeamento. Não selecione **Meus dados incluem informações de roteamento**.

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Table** | *DiagnosticRawRecords* | A tabela criada no banco de dados *TestDatabase*. |
    | **Formato dos dados** | *JSON* | O formato usado na tabela. |
    | **Mapeamento de coluna** | *DiagnosticRawRecordsMapping* | O mapeamento criado no banco de dados *TestDatabase*, que mapeia os dados JSON de entrada para os nomes de coluna e os tipos de dados da tabela *DiagnosticRawRecords*.|
    | | |

1. Selecione **Criar**.  

# <a name="activity-logs"></a>[Logs de atividade](#tab/activity-logs)

1. Use as seguintes configurações na janela **Conexão de Dados**:

    Fonte de dados:

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da conexão de dados** | *ActivityLogsConnection* | O nome da conexão que você deseja criar no Azure Data Explorer.|
    | **Namespace do hub de eventos** | *AzureMonitoringData* | O nome escolhido anteriormente que identifica seu namespace. |
    | **Hub de eventos** | *insights-operational-logs* | O hub de eventos que você criou. |
    | **Grupo de consumidores** | *$Default* | O grupo de consumidor padrão. Se necessário, é possível criar um grupo de consumidor diferente. |
    | | |

    Tabela de destino:

    Há duas opções de roteamento: *estático* e *dinâmico*. Para este tutorial, você usará o roteamento estático (o padrão), no qual especificará o nome da tabela, o formato de dados e o mapeamento. Não selecione **Meus dados incluem informações de roteamento**.

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Table** | *ActivityLogsRawRecords* | A tabela criada no banco de dados *TestDatabase*. |
    | **Formato dos dados** | *JSON* | O formato usado na tabela. |
    | **Mapeamento de coluna** | *ActivityLogsRawRecordsMapping* | O mapeamento criado no banco de dados *TestDatabase*, que mapeia os dados JSON de entrada para os nomes de coluna e os tipos de dados da tabela *ActivityLogsRawRecords*.|
    | | |

1. Selecione **Criar**.  
---

## <a name="query-the-new-tables"></a>Consultar as novas tabelas

Agora você tem um pipeline com os dados fluindo. A ingestão por meio do cluster leva 5 minutos por padrão; portanto, permita que os dados fluam por alguns minutos antes de iniciar a consulta.

# <a name="diagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Consultar a tabela de métricas de diagnóstico

A seguinte consulta analisa os dados de duração da consulta dos registros da métrica de diagnóstico no Azure Data Explorer:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Resultados da consulta:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logs"></a>[Logs de diagnóstico](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Consultar a tabela dos logs de diagnóstico

Esse pipeline produz ingestões por meio de um hub de eventos. Você examinará os resultados dessas ingestões.
A consulta a seguir analisa quantas ingestões são acumuladas em um minuto, incluindo uma amostra de `Database`, `Table` e `IngestionSourcePath` para cada intervalo:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Resultados da consulta:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logs"></a>[Logs de atividade](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Consultar a tabela de logs de atividades

A seguinte consulta analisa os dados dos registros do log de atividades no Azure Data Explorer:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Resultados da consulta:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>Próximas etapas

* Saiba como escrever muito mais consultas nos dados extraídos do Azure Data Explorer usando [Escrever consultas para o Azure Data Explorer](write-queries.md).
* [Monitorar operações de ingestão do Azure Data Explorer usando logs de diagnóstico](using-diagnostic-logs.md)
* [Usar métricas para monitorar a integridade do cluster](using-metrics.md)
