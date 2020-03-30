---
title: Monitore aplicativos lógicos usando logs do Azure Monitor
description: Soluciona problemas de aplicativos lógicos configurando logs do Monitor do Azure e coletando dados de diagnóstico para aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270232"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Configure registros do Monitor do Azure e colete dados de diagnóstico para aplicativos de lógica do Azure

Para obter informações mais ricas de depuração sobre seus aplicativos lógicos durante o tempo de execução, você pode configurar e usar [registros do Azure Monitor](../azure-monitor/platform/data-platform-logs.md) para registrar e armazenar informações sobre dados e eventos de tempo de execução, como eventos de gatilho, eventos de execução e eventos de ação em um espaço de trabalho do Log [Analytics.](../azure-monitor/platform/resource-logs-collect-workspace.md) [O Azure Monitor](../azure-monitor/overview.md) ajuda você a monitorar seus ambientes em nuvem e no local para que você possa manter mais facilmente sua disponibilidade e desempenho. Ao usar os registros do Azure Monitor, você pode criar [consultas de log](../azure-monitor/log-query/log-query-overview.md) que ajudam a coletar e revisar essas informações. Você também pode [usar esses dados de diagnóstico com outros serviços do Azure,](#extend-data)como o Azure Storage e o Azure Event Hubs.

Para configurar o registro para o seu aplicativo lógico, você pode [ativar o Log Analytics quando criar seu aplicativo lógico](#logging-for-new-logic-apps)ou instalar a solução Logic Apps [Management](#install-management-solution) em seu espaço de trabalho Log Analytics para aplicativos lógicos existentes. Essa solução fornece informações agregadas para o aplicativo lógico executado e inclui detalhes específicos, como status, tempo de execução, status de reenvio e IDs de correlação. Em seguida, para habilitar o registro e a criação de consultas para essas informações, [configure os logs do Monitor do Azure](#set-up-resource-logs).

Este artigo mostra como ativar o Log Analytics quando você cria aplicativos lógicos, como instalar e configurar a solução Logic Apps Management e como configurar e criar consultas para logs do Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa de um [espaço de trabalho do Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). Se você não tem um espaço de trabalho, [aprenda a criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Habilite o Log Analytics para novos aplicativos lógicos

Você pode ativar o Log Analytics ao criar seu aplicativo lógico.

1. No [portal Azure,](https://portal.azure.com)no painel onde você fornece as informações para criar seu aplicativo lógico, siga estas etapas:

   1. Em **Log Analytics,** selecione **On**.

   1. Na lista **de espaço de trabalho do Log Analytics,** selecione o espaço de trabalho para onde deseja enviar os dados do aplicativo lógico executado.

      ![Fornecer informações de aplicativo lógico](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Após a conclusão dessa etapa, o Azure criará o aplicativo lógico, que agora está associado ao espaço de trabalho do Log Analytics. Além disso, esta etapa instala automaticamente a solução Logic Apps Management em seu espaço de trabalho.

1. Quando terminar, selecione **Criar**.

1. Depois de executar seu aplicativo lógico, para ver suas corridas lógicas, [continue com essas etapas](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Instalar a solução de Gerenciamento dos Aplicativos Lógicos

Se você ligou o Log Analytics quando criou seu aplicativo lógico, pule esse passo. Você já tem a solução Logic Apps Management instalada no seu espaço de trabalho Log Analytics.

1. Na caixa de pesquisa do portal `log analytics workspaces` [Azure,](https://portal.azure.com)digite e selecione espaços de trabalho do Log **Analytics**.

   ![Selecione "Espaços de trabalho do Log Analytics"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. Em **Log Analytics,** selecione seu espaço de trabalho.

   ![Selecionar o espaço de trabalho do Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. No **painel Visão geral,** em **Iniciar com o Log Analytics** > **Configure soluções de monitoramento,** selecione **Exibir soluções**.

   ![No painel de visão geral, selecione "Ver soluções"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Em **Visão Geral,** selecione **Adicionar**.

   ![No painel de visão geral, adicione uma nova solução](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Depois que o **Marketplace abrir,** `logic apps management`na caixa de pesquisa, digite e selecione **Logic Apps Management**.

   ![No Marketplace, selecione "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. No painel de descrição da solução, selecione **Criar**.

   ![Selecione "Criar" para adicionar a solução "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Revise e confirme o espaço de trabalho do Log Analytics onde deseja instalar a solução e selecione **Criar** novamente.

   ![Selecione "Criar" para "Gerenciamento de Aplicativos Lógicos"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Depois que o Azure implanta a solução para o grupo de recursos Do Azure que contém seu espaço de trabalho do Log Analytics, a solução aparece no painel de resumo do seu espaço de trabalho.

   ![Painel de resumo do espaço de trabalho](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurar os logs do Azure Monitor

Quando você armazena informações sobre eventos e dados em tempo de execução nos [registros do Azure Monitor,](../azure-monitor/platform/data-platform-logs.md)você pode criar [consultas de log](../azure-monitor/log-query/log-query-overview.md) que ajudam a encontrar e revisar essas informações.

1. No [portal do Azure](https://portal.azure.com), encontre e selecione o aplicativo lógico.

1. No menu do aplicativo lógico, em **Monitoramento,** selecione **Configurações** > de diagnóstico**Adicionar configuração de diagnóstico**.

   ![Em "Monitoramento", selecione "Configurações de diagnóstico" > "Adicionar configuração de diagnóstico"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Para criar a configuração, siga estas etapas:

   1. Forneça um nome para a configuração.

   1. Selecione **Enviar para o Log Analytics**.

   1. Para **assinatura**, selecione a assinatura do Azure associada ao seu espaço de trabalho do Log Analytics.

   1. Para **o Log Analytics Workspace,** selecione o espaço de trabalho que deseja usar.

   1. Em **log,** selecione a categoria **WorkflowRuntime,** que especifica a categoria de evento que você deseja gravar.

   1. Para selecionar todas as métricas, em **métrica,** selecione **AllMetrics**.

   1. Quando terminar, selecione **Salvar**.

   Por exemplo: 

   ![Selecionar o espaço de trabalho do Log Analytics e os dados para registrar em log](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Exibir o status de executa o aplicativo lógico

Depois que seu aplicativo de lógica é executado, você pode visualizar os dados sobre essas corridas no espaço de trabalho do Log Analytics.

1. No [portal Azure,](https://portal.azure.com)encontre e abra seu espaço de trabalho do Log Analytics.

1. No menu do seu espaço de trabalho, selecione Gerenciamento de**aplicativos** **lógicos de** > resumo do espaço de trabalho .

   ![Status e contagem de execuções do aplicativo lógico](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Se o bloco logic apps management não mostrar imediatamente os resultados após uma execução, tente selecionar **Atualizar** ou esperar por um curto período de tempo antes de tentar novamente.

   Aqui, as execuções de seu aplicativo lógica são agrupadas por nome ou por status de execução. Esta página também mostra detalhes sobre falhas em ações ou gatilhos para executar o aplicativo lógico.

   ![Resumo do status das execuções de seu aplicativo lógico](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Para ver todas as corridas de um aplicativo ou status de lógica específica, selecione a linha para esse aplicativo ou status lógico.

   Veja um exemplo que mostra todas as execuções de um aplicativo lógico específico:

   ![Exibir corridas e status de aplicativos lógicos](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   Para ações em que você [configura propriedades rastreadas,](#extend-data)você também pode visualizar essas propriedades selecionando **Exibir** na coluna **Propriedades Rastreadas.** Para pesquisar as propriedades rastreadas, use o filtro de coluna.

   ![Exibir propriedades rastreadas para um aplicativo lógico](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Propriedades rastreadas ou eventos concluídos podem sofrer atrasos de 10 a 15 minutos antes de aparecer em seu espaço de trabalho do Log Analytics.
   > Além disso, o recurso **Reenviar** nesta página está indisponível no momento.

1. Para filtrar seus resultados, você pode executar a filtragem do lado do cliente e do servidor.

   * **Filtro do lado do cliente**: Para cada coluna, selecione os filtros que deseja, por exemplo:

     ![Exemplo de filtros de coluna](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filtro do lado do servidor**: Para selecionar uma janela de tempo específica ou para limitar o número de corridas que aparecem, use o controle de escopo na parte superior da página. Por padrão, apenas 1.000 registros aparecem por vez.

     ![Alterar o período](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Para visualizar todas as ações e seus detalhes para uma execução específica, selecione a linha para uma execução de aplicativo lógico.

   Aqui está um exemplo que mostra todas as ações e gatilhos para uma execução específica do aplicativo lógico:

   ![Exibir ações para uma execução de aplicativo lógico](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Envie dados de diagnóstico para o Azure Storage e a Azure Event Hubs

Juntamente com os logs do Azure Monitor, você pode estender a forma como você usa os dados de diagnóstico do seu aplicativo lógico com outros serviços do Azure, por exemplo:

* [Arquivar logs de recursos do Azure na conta de armazenamento](../azure-monitor/platform/resource-logs-collect-storage.md)
* [A plataforma Stream Azure faz logs no Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Depois, obtenha o monitoramento em tempo real usando a telemetria e a análise de outros serviços, como o [Stream Analytics do Azure](../stream-analytics/stream-analytics-introduction.md) e o [Power BI](../azure-monitor/platform/powerbi.md). Por exemplo: 

* [Transmitir dados dos Hubs de Eventos para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analisar dados de streaming com o Stream Analytics e criar um painel de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nos locais onde você deseja enviar dados de diagnóstico, certifique-se de criar primeiro [uma conta de armazenamento Azure](../storage/common/storage-create-storage-account.md) ou criar um hub de eventos [Azure](../event-hubs/event-hubs-create.md). Em seguida, você pode selecionar os destinos onde deseja enviar esses dados. Os períodos de retenção só se aplicam quando você usa uma conta de armazenamento.

![Enviar dados para uma conta de armazenamento do Azure ou um hub de eventos](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Eventos de diagnóstico do Azure Monitor

Cada evento de diagnóstico contém detalhes sobre o aplicativo lógico e o evento, por exemplo, o status, a hora de início, a hora de término e assim por diante. Para configurar programáticamente o monitoramento, o rastreamento e o registro, você pode usar essas informações com a [API REST para Aplicativos lógicos azure](https://docs.microsoft.com/rest/api/logic) e a [API REST para Monitor Azure](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows). Você também pode `clientTrackingId` `trackedProperties` usar as propriedades e, que aparecem em 

* `clientTrackingId`: se não for fornecido, o Azure gerará essa ID automaticamente e correlacionará eventos em uma execução do aplicativo lógico, incluindo os fluxos de trabalho aninhados que são chamados no aplicativo lógico. Você pode especificar manualmente este ID `x-ms-client-tracking-id` em um gatilho passando um cabeçalho com seu valor de ID personalizado na solicitação de gatilho. Use um gatilho de solicitação, gatilho HTTP ou gatilho de webhook.

* `trackedProperties`: Para rastrear entradas ou saídas em dados `trackedProperties` de diagnóstico, você pode adicionar uma seção a uma ação usando o Logic App Designer ou diretamente na definição JSON do seu aplicativo lógico. As propriedades acompanhadas só podem acompanhar entradas e saídas de uma única ação, mas você pode usar as propriedades `correlation` dos eventos para correlacionar entre as ações em uma execução. Para rastrear mais de uma propriedade, uma `trackedProperties` ou mais propriedades, adicione a seção e as propriedades que deseja para a definição de ação.

  Aqui está um exemplo que mostra como a definição de ação **variável Initialize** inclui propriedades rastreadas a partir da entrada da ação onde a entrada é uma matriz, não um registro.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  Este exemplo mostra várias propriedades rastreadas:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Este exemplo mostra `ActionCompleted` como o `clientTrackingId` `trackedProperties` evento inclui os e atributos:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Criar consultas de monitoramento e acompanhamento](../logic-apps/create-monitoring-tracking-queries.md)
* [Monitorar mensagens B2B com os logs do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)
