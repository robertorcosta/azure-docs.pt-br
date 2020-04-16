---
title: Monitorar data factories usando o Azure Monitor
description: Saiba como usar o Azure Monitor para monitorar os pipelines da Fábrica de Dados do Azure, permitindo registros de diagnóstico com informações da Fábrica de Dados.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 5753336eeef115038de4eb0b5ade0651b1fa293e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419452"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Alerte e monitore fábricas de dados usando o Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

As aplicações em nuvem são complexas e têm muitas partes móveis. Os monitores fornecem dados para ajudar a garantir que seus aplicativos permaneçam em funcionamento em um estado saudável. Os monitores também ajudam a evitar problemas potenciais e solucionar problemas anteriores.

Você pode usar dados de monitoramento para obter insights profundos sobre seus aplicativos. Esse conhecimento ajuda a melhorar o desempenho e a manutenção do aplicativo. Também ajuda a automatizar ações que de outra forma requerem intervenção manual.

O Azure Monitor fornece métricas e logs de infra-estrutura de nível básico para a maioria dos serviços do Azure. Os logs de diagnóstico do Azure são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. E a Fábrica de Dados Do Azure grava registros de diagnóstico no Monitor.

Para obter detalhes, consulte [a visão geral do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Mantendo os dados da Fábrica de Dados do Azure

A Fábrica de Dados armazena dados de pipeline por apenas 45 dias. Use monitor se quiser manter esses dados por mais tempo. Com o Monitor, você pode encaminhar registros de diagnóstico para análise. Você também pode mantê-los em uma conta de armazenamento para que você tenha informações de fábrica durante a duração escolhida.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

* Salve seus registros de diagnóstico em uma conta de armazenamento para auditoria ou inspeção manual. Você pode usar as configurações de diagnóstico para especificar o tempo de retenção em dias.
* Transmita os registros para o Azure Event Hubs. Os logs tornam-se entrada para um serviço de parceiro ou para uma solução de análise personalizada como o Power BI.
* Analise os logs com o Log Analytics.

Você pode usar uma conta de armazenamento ou um namespace do hub de eventos que não esteja na assinatura do recurso que emite logs. O usuário que configura a configuração deve ter acesso adequado ao RBAC (Role-Based Access Control, controle de acesso baseado em função) a ambas as assinaturas.

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

### <a name="diagnostic-settings"></a>Configurações de Diagnóstico

Use configurações de diagnóstico para configurar registros de diagnóstico para recursos não computacionais. As configurações para um controle de recursos têm os seguintes recursos:

* Eles especificam para onde os registros de diagnóstico são enviados. Exemplos incluem uma conta de armazenamento Azure, um hub de eventos Do Zure ou registros do Monitor.
* Eles especificam quais categorias de log são enviadas.
* Eles especificam por quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento.
* Uma retenção de zero dias significa que os logs serão mantidos indefinidamente. Caso contrário, o valor pode ser de qualquer número de dias de 1 a 2.147.483.647.
* Se as políticas de retenção forem definidas, mas o armazenamento de logs em uma conta de armazenamento for desativado, as políticas de retenção não terão efeito. Por exemplo, essa condição pode acontecer quando apenas opções de hubs de eventos ou logs do Monitor são selecionadas.
* As políticas de retenção são aplicadas por dia. A fronteira entre os dias ocorre à meia-noite no Horário Universal Coordenado (UTC). No final de um dia, os registros de dias que estão além da política de retenção são excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início de hoje os registros de antes de ontem são excluídos.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Habilite registros de diagnóstico através da API Rest do Monitor Do Azure

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Crie ou atualize uma configuração de diagnóstico na API Monitor REST

##### <a name="request"></a>Solicitação

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* Substitua `{api-version}` por `2016-09-01`.
* Substitua pelo `{resource-id}` ID do recurso para o qual deseja editar as configurações de diagnóstico. Para obter mais informações, consulte [Usando grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Defina o cabeçalho `Content-Type` como `application/json`.
* Defina o cabeçalho de autorização para o token web JSON que você recebeu do Azure Active Directory (Azure AD). Para obter mais informações, consulte [Solicitações de autenticação](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Corpo

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Propriedade | Type | Descrição |
| --- | --- | --- |
| **armazenamentoAccountId** |String | O ID de recurso da conta de armazenamento para a qual você deseja enviar registros de diagnóstico. |
| **serviceBusRuleId** |String | A regra de ponto de serviço-ônibus iD do espaço de nome de ponto de serviço-ônibus no qual você deseja ter Hubs de eventos criados para logs de diagnóstico de streaming. O ID da `{service bus resource ID}/authorizationrules/{key name}`regra tem o formato .|
| **espaço de trabalhoId** | Tipo complexo | Uma matriz de grãos de tempo métricos e suas políticas de retenção. O valor desta propriedade está vazio. |
|**Métricas**| Valores de parâmetro da execução do pipeline a serem passados para o pipeline invocado| Um objeto JSON que mapeia nomes de parâmetros para valores de argumento. |
| **Logs**| Tipo complexo| O nome de uma categoria de registro de diagnóstico para um tipo de recurso. Para obter a lista de categorias de registro de diagnóstico para um recurso, execute uma operação de configurações de diagnóstico GET. |
| **Categoria**| String| Uma série de categorias de log e suas políticas de retenção. |
| **timeGrain** | String | A granularidade das métricas, que são capturadas no formato de duração ISO 8601. O valor da `PT1M`propriedade deve ser , o que especifica um minuto. |
| **Habilitado**| Boolean | Especifica se a coleta da categoria métrica ou log está habilitada para este recurso. |
| **retentionPolicy**| Tipo complexo| Descreve a política de retenção para uma categoria de métrica ou de log. Esta propriedade é usada apenas para contas de armazenamento. |
|**Dias**| Int| O número de dias para manter as métricas ou logs. Se o valor da propriedade for 0, os registros serão mantidos para sempre. Esta propriedade é usada apenas para contas de armazenamento. |

##### <a name="response"></a>Resposta

200 OK.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Obtenha informações sobre as configurações de diagnóstico na API Monitor REST

##### <a name="request"></a>Solicitação

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* Substitua `{api-version}` por `2016-09-01`.
* Substitua pelo `{resource-id}` ID do recurso para o qual deseja editar as configurações de diagnóstico. Para obter mais informações, consulte [Usando grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Defina o cabeçalho `Content-Type` como `application/json`.
* Defina o cabeçalho de autorização para um token web JSON que você recebeu do Azure AD. Para obter mais informações, consulte [Solicitações de autenticação](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Resposta

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
Para obter mais informações, consulte [Configurações de diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Esquema de registros e eventos

### <a name="monitor-schema"></a>Esquema do monitor

#### <a name="activity-run-log-attributes"></a>Atributos de log de execução de atividade

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propriedade | Type | Descrição | Exemplo |
| --- | --- | --- | --- |
| **Nível** |String | O nível dos registros de diagnóstico. Para registros de execução de atividades, defina o valor da propriedade como 4. | `4` |
| **Correlationid** |String | O iD exclusivo para rastrear uma solicitação específica. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z`TIMESPAN UTC . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| A id da atividade é executada. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| A id do oleoduto funcionar. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| String | O ID associado ao recurso de fábrica de dados. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Categoria**| String | A categoria dos registros diagnósticos. Defina o valor da propriedade `ActivityRuns`. | `ActivityRuns` |
|**Nível**| String | O nível dos registros de diagnóstico. Defina o valor da propriedade `Informational`. | `Informational` |
|**Operationname**| String | O nome da atividade com seu status. Se a atividade é o batimento `MyActivity -`cardíaco inicial, o valor da propriedade é . Se a atividade é o batimento `MyActivity - Succeeded`cardíaco final, o valor da propriedade é . | `MyActivity - Succeeded` |
|**pipelineName**| String | O nome do oleoduto. | `MyPipeline` |
|**activityName**| String | O nome da atividade. | `MyActivity` |
|**start**| String | O tempo de início da atividade é executado no formato TIMEPAN UTC. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | O tempo final da atividade é executado no formato TIMEPan UTC. Se o registro de diagnóstico mostrar que uma atividade foi `1601-01-01T00:00:00Z`iniciada, mas ainda não encerrada, o valor do imóvel será . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Atributos de log executados por pipeline

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propriedade | Type | Descrição | Exemplo |
| --- | --- | --- | --- |
| **Nível** |String | O nível dos registros de diagnóstico. Para registros de execução de atividades, defina o valor da propriedade como 4. | `4` |
| **Correlationid** |String | O iD exclusivo para rastrear uma solicitação específica. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z`TIMESPAN UTC . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| A id do oleoduto funcionar. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| String | O ID associado ao recurso de fábrica de dados. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Categoria**| String | A categoria dos registros diagnósticos. Defina o valor da propriedade `PipelineRuns`. | `PipelineRuns` |
|**Nível**| String | O nível dos registros de diagnóstico. Defina o valor da propriedade `Informational`. | `Informational` |
|**Operationname**| String | O nome do oleoduto junto com seu status. Depois que a execução do `Pipeline - Succeeded`gasoduto é concluída, o valor da propriedade é . | `MyPipeline - Succeeded`. |
|**pipelineName**| String | O nome do oleoduto. | `MyPipeline` |
|**start**| String | O tempo de início da atividade é executado no formato TIMEPAN UTC. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | O tempo final da atividade é executado no formato TIMEPan UTC. Se o registro de diagnóstico mostrar que uma atividade `1601-01-01T00:00:00Z`foi iniciada, mas ainda não terminou, o valor da propriedade será .  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | O status final do oleoduto. Possíveis valores `Succeeded` `Failed`de propriedade são e . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Atributos de log de execução de gatilho

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Propriedade | Type | Descrição | Exemplo |
| --- | --- | --- | --- |
| **Nível** |String | O nível dos registros de diagnóstico. Para registros de execução de atividades, defina o valor da propriedade como 4. | `4` |
| **Correlationid** |String | O iD exclusivo para rastrear uma solicitação específica. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z`TIMESPAN UTC . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| A id do gatilho é executada. | `08587023010602533858661257311` |
|**Resourceid**| String | O ID associado ao recurso de fábrica de dados. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Categoria**| String | A categoria dos registros diagnósticos. Defina o valor da propriedade `PipelineRuns`. | `PipelineRuns` |
|**Nível**| String | O nível dos registros de diagnóstico. Defina o valor da propriedade `Informational`. | `Informational` |
|**Operationname**| String | O nome do gatilho com seu status final, o que indica se o gatilho disparou com sucesso. Se o batimento cardíaco foi `MyTrigger - Succeeded`bem sucedido, o valor da propriedade é . | `MyTrigger - Succeeded` |
|**Triggername**| String | O nome do gatilho. | `MyTrigger` |
|**triggerType**| String | O tipo do gatilho. Possíveis valores `Manual Trigger` `Schedule Trigger`de propriedade são e . | `ScheduleTrigger` |
|**triggerEvent**| String | O evento do gatilho. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | O tempo de início do disparo do gatilho no formato UTC timepan. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | O estado final mostrando se o gatilho disparou com sucesso. Possíveis valores `Succeeded` `Failed`de propriedade são e . | `Succeeded`|

### <a name="log-analytics-schema"></a>Esquema de análise de log

O Log Analytics herda o esquema do Monitor com as seguintes exceções:

* A primeira letra em cada nome de coluna é capitalizada. Por exemplo, o nome da coluna "correlationId" no Monitor é "CorrelationId" no Log Analytics.
* Não há coluna "Nível".
* A coluna dinâmica "propriedades" é preservada como o seguinte tipo dinâmico de bolha JSON.

    | Coluna Monitor azure | Coluna Log Analytics | Type |
    | --- | --- | --- |
    | Propriedades de $. Propriedades do usuário | UserProperties | Dinâmico |
    | Propriedades de $. Anotações | Anotações | Dinâmico |
    | Propriedades de $. Entrada | Entrada | Dinâmico |
    | Propriedades de $. Saída | Saída | Dinâmico |
    | Propriedades de $. Erro.erroCódigo | ErrorCode | INT |
    | Propriedades de $. Erro.mensagem | ErrorMessage | string |
    | Propriedades de $. Erro | Erro | Dinâmico |
    | Propriedades de $. Antecessores | Antecessores | Dinâmico |
    | Propriedades de $. Parâmetros | Parâmetros | Dinâmico |
    | Propriedades de $. Systemparameters | SystemParameters | Dinâmico |
    | Propriedades de $. Tags | Marcas | Dinâmico |
    
## <a name="metrics"></a>Métricas

Com o Monitor, você pode ganhar visibilidade sobre o desempenho e a saúde de suas cargas de trabalho do Azure. O tipo mais importante de dados do Monitor é a métrica, que também é chamada de contador de desempenho. As métricas são emitidas pela maioria dos recursos do Azure. O Monitor fornece várias maneiras de configurar e consumir essas métricas para monitoramento e solução de problemas.

A versão 2 do Azure Data Factory emite as seguintes métricas.

| **Métrica**           | **Nome de exibição métrica**         | **Unidade** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Métricas de execução do pipeline bem-sucedido | Contagem    | Total                | O número total de corridas de gasodutos que tiveram sucesso dentro de uma janela de um minuto. |
| PipelineFailedRuns   | Métricas de execução do pipeline com falha    | Contagem    | Total                | O número total de operações de gasodutos que falharam dentro de uma janela de um minuto.    |
| ActivitySucceededRuns | Métricas de execução de atividades bem-sucedidas | Contagem    | Total                | O número total de atividades é executado dentro de uma janela de um minuto.  |
| ActivityFailedRuns   | Métricas de execução de atividades com falha    | Contagem    | Total                | O número total de atividades é executado que falhou dentro de uma janela de um minuto.     |
| TriggerSucceededRuns | Métricas de execuções do gatilho bem-sucedidas  | Contagem    | Total                | O número total de corridas de gatilho que teve sucesso dentro de uma janela de um minuto.   |
| TriggerFailedRuns    | Métricas de execuções do gatilho com falha     | Contagem    | Total                | O número total de acionadores que falharam dentro de uma janela de um minuto.      |

Para acessar as métricas, complete as instruções na [plataforma de dados Do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Somente são emitidos eventos completos, de atividade saqueada e de corridas de dutos. Em andamento, as corridas de caixa de areia/depuração **não** são emitidas. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitore as métricas da fábrica de dados com o Monitor Do Azure

Você pode usar a integração data factory com o Monitor para encaminhar dados para o Monitor. Essa integração é útil nos seguintes cenários:

* Você deseja escrever consultas complexas em um rico conjunto de métricas que é publicada pela Data Factory para monitorar. Você pode criar alertas personalizados nessas consultas via Monitor.

* Você quer monitorar os data factories. Você pode direcionar dados de várias fábricas de dados para um único espaço de trabalho do Monitor.

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Configure as configurações de diagnóstico e o espaço de trabalho

Crie ou adicione configurações de diagnóstico para sua fábrica de dados.

1. No portal, vá para Monitor. Selecione **Configurações** > **De diagnóstico .**

1. Selecione a fábrica de dados para a qual deseja definir uma configuração de diagnóstico.

1. Se não houver configurações na fábrica de dados selecionada, você será solicitado a criar uma configuração. Selecione **Ativar diagnóstico**.

   ![Crie uma configuração de diagnóstico se não existirem configurações](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se houver configurações existentes na fábrica de dados, você verá uma lista de configurações já configuradas na fábrica de dados. Selecione **Adicionar configuração de diagnóstico**.

   ![Adicione uma configuração de diagnóstico se existirem configurações](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Dê à sua configuração um nome, selecione **Enviar para O Log Analytics**e, em seguida, selecione um espaço de trabalho no Log **Analytics Workspace**.

    ![Nomeie suas configurações e selecione um espaço de trabalho de análise de log](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Clique em **Salvar**.

Após alguns momentos, a nova configuração aparece em sua lista de configurações para esta fábrica de dados. Os registros de diagnóstico são transmitidos para esse espaço de trabalho assim que novos dados de eventos são gerados. Até 15 minutos podem se passar entre quando um evento é emitido e quando ele aparece no Log Analytics.

* No _modo específico de recursos,_ os logs de diagnóstico do fluxo da Fábrica de Dados do Azure para as tabelas _ADFPipelineRun,_ _ADFTriggerRun_e _ADFActivityRun_
* No modo _Diagnóstico do Azure_, os logs de diagnóstico fluem para a tabela _AzureDiagnostics_

> [!NOTE]
> Como uma tabela de registro do Azure não pode ter mais de 500 colunas, recomendamos que você selecione o modo Específico de Recursos. Para obter mais informações, consulte [As limitações conhecidas do Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instale o Azure Data Factory Analytics pelo Azure Marketplace

![Vá para "Azure Marketplace", digite "Analytics filter" e selecione "Azure Data Factory Analytics (Preview")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Detalhes sobre "Azure Data Factory Analytics (Preview)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Selecione **Criar** e, em seguida, selecione **as configurações** **do espaço de trabalho** do OMS e do OMS .

![Criando uma nova solução](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorar métricas da fábrica de dados

A instalação do Azure Data Factory Analytics cria um conjunto padrão de visualizações para que as seguintes métricas sejam ativadas:

- ADF Runs - 1) Pipeline Funciona por Fábrica de Dados
 
- Execuções do ADF – 2) Execuções de atividade por Data Factory

- ADF Runs - 3) Trigger Runs by Data Factory

- Erros de ADF - 1) Top 10 Erros de pipeline por fábrica de dados

- Erros de ADF - 2) Top 10 Atividades Executadas por Fábrica de Dados

- Erros de ADF - 3) Top 10 Erros de gatilho por fábrica de dados

- Estatísticas adf - 1) Atividade executada por tipo

- Estatísticas adf - 2) Trigger Runs by Type

- Estatísticas aDF - 3) Max Pipeline executa duração

![Janela com "Workbooks (Preview)" e "AzureDataFactoryAnalytics" destacadas](media/data-factory-monitor-oms/monitor-oms-image6.png)

Você pode visualizar as métricas anteriores, olhar as consultas por trás dessas métricas, editar as consultas, criar alertas e tomar outras ações.

![Representação gráfica do pipeline executado por fábrica de dados"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> A Visualização (Azure Data Factory Analytics, análise de fábrica de dados do zure) envia registros de diagnóstico para tabelas de destino _específicas de recursos._ Você pode escrever consultas nas seguintes tabelas: _ADFPipelineRun,_ _ADFTriggerRun_e _ADFActivityRun_.

## <a name="alerts"></a>Alertas

Faça login no portal Azure e selecione**Alertas de** **Monitor** > para criar alertas.

![Alertas no menu do portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Criar alertas

1. Selecione **+ Nova regra de alerta** para criar um novo alerta.

    ![Nova regra de alerta](media/monitor-using-azure-monitor/alerts_image4.png)

1. Defina a condição de alerta.

    > [!NOTE]
    > Certifique-se de selecionar **Tudo** no Filtro por lista de sossede do tipo de **recurso.**

    !["Definir condição de alerta" > "Selecionar alvo", que abre o painel "Selecionar um recurso" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definir condição de alerta" >" Adicionar critérios", que abre o painel "Configurar lógica de sinal"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Painel "Configurar tipo de sinal"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Defina os detalhes do alerta.

    ![Detalhes do Alerta](media/monitor-using-azure-monitor/alerts_image8.png)

1. Defina o grupo de ação.

    ![Crie uma regra, com "Grupo Nova Ação" destacado](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Criar um novo grupo de ações](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Configure e-mail, SMS, push e voz](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Defina um grupo de ação](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Próximas etapas
[Monitore e gerencie os gasodutos de forma programática](monitor-programmatically.md)
