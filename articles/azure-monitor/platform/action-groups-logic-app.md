---
title: Como disparar ações complexas com alertas do Azure Monitor
description: Saiba como criar uma ação do aplicativo lógico para processar alertas do Azure Monitor.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 0abc7c4ae370a894c46eda38df41d64a6ef2cd91
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027711"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Como disparar ações complexas com alertas do Azure Monitor

Este artigo mostra como configurar e disparar um aplicativo lógico para criar uma conversa no Microsoft Teams quando um alerta é acionado.

## <a name="overview"></a>Visão geral
Quando um alerta do Azure Monitor dispara, ele chama um [grupo de ações](../../azure-monitor/platform/action-groups.md). Os grupos de ações permitem que você dispare uma ou mais ações para notificar os outros sobre o alerta e até mesmo corrigi-lo.

O processo geral é:

-   Criar o Aplicativo Lógico para o respectivo tipo de alerta

-   Importe um conteúdo de exemplo para o respectivo tipo de alerta para o aplicativo lógico.

-   Definir o comportamento do Aplicativo Lógico

-   Copiar o ponto de extremidade HTTP do aplicativo lógico em um grupo de ação do Microsoft Azure.

O processo será semelhante se você quiser que o aplicativo lógico execute uma ação diferente.

## <a name="create-an-activity-log-alert-administrative"></a>Criar um alerta do log de atividades: Administrativo

1.  No portal do Microsoft Azure, selecione **Criar um recurso** no canto superior esquerdo.

2.  Pesquise e selecione **Aplicativo Lógico**, sem seguida selecione **Criar**.

3.  Forneça um **Nome** ao Aplicativo Lógico, escolha um **Grupo de recursos** e etc.

    ![Criar um aplicativo lógico](media/action-groups-logic-app/create-logic-app-dialog.png "Criar um aplicativo lógico")

4.  Selecione **Criar** para criar o aplicativo lógico. Uma mensagem de pop-up indica que um aplicativo lógico foi criado. Selecione **Recursos de Lançamento** para abrir o **Designer de Aplicativos Lógicos**.

5.  Selecionar o gatilho: **Quando uma solicitação HTTP for recebida**.

    ![Gatilhos do Aplicativo Lógico](media/action-groups-logic-app/logic-app-triggers.png "Gatilhos do Aplicativo Lógico")

6.  Selecione **Editar** para alterar o gatilho de solicitação HTTP.

    ![HTTP solicita gatilhos](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP solicita gastilhos")

7.  Selecione **Use o conteúdo de amostra para gerar o esquema**.

    ![Use uma carga de exemplo](media/action-groups-logic-app/use-sample-payload-button.png "Use uma carga de exemplo")

8.  Copie e cole o seguinte conteúdo de exemplo na caixa de diálogo:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. O **Designer do Aplicativo Lógico** exibirá uma janela pop-up lembrando que a solicitação enviada ao Aplicativo Lógico deve definir o cabeçalho **Content-Type** para **aplicativo/json**. Fechar a janela pop-up. O alerta do Azure Monitor define o cabeçalho.

    ![Defina o cabeçalho do Content-Type](media/action-groups-logic-app/content-type-header.png "Definir o cabeçalho Content-Type")

10. Selecione **+** **Nova Etapa** e, em seguida, **Adicione uma ação**.

    ![Adicione uma ação](media/action-groups-logic-app/add-action.png "Adicione uma ação")

11. Pesquise e selecione o conector do Microsoft Teams. Escolha a ação **Microsoft Teams – Postar Mensagem**.

    ![Ações do Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Ações do Microsoft Teams")

12. Configure a ação do Microsoft Teams. O **Designer de Aplicativos Lógicos** solicitará que você autentique-se na sua conta do Office365. Escolha a **ID da Equipe** e **ID do Canal** para enviar a mensagem.

13. Configure a mensagem usando uma combinação de texto estático e referências aos \<campos\> no conteúdo dinâmico. Recorte e cole o texto a seguir no campo **Mensagem**.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Em seguida, localize e substitua os \<campos\> por marcar de conteúdo dinâmico com o mesmo nome.

    > [!NOTE]
    > Há dois campos dinâmicos nomeados **status**. Adicione ambos os campos à mensagem. Use um no recipiente de propriedades **activityLog** e exclua o outro campo. Passe o cursor sobre o campo **status** para ver a referência de campo completa, conforme mostrado na captura de tela a seguir:

    ![Ação do Microsoft Teams: Enviar uma mensagem](media/action-groups-logic-app/teams-action-post-message.png "ação Microsoft Teams: Enviar uma mensagem")

14. Na parte superior do **Designer de Aplicativos Lógicos**, selecione **Salvar** para salvar seu aplicativo lógico.

15. Abra o grupo de ações existente e adicione uma ação para referenciar o aplicativo lógico. Se você não tiver um grupo de ação existente, consulte [Criar e gerenciar grupos de ação no Portal do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) para criar um. Não se esqueça de salvar as alterações.

    ![Atualizar grupo de ações](media/action-groups-logic-app/update-action-group.png "Atualizar grupo de ações")

Na próxima vez que um alerta chamar o grupo de ações, o aplicativo lógico será chamado.

## <a name="create-a-service-health-alert"></a>Criar um alerta de integridade do serviço

As entradas da Integridade de Serviço do Azure são parte do registro de atividade. O processo para criar o alerta é semelhante para [criar um alerta de registro de atividade](#create-an-activity-log-alert-administrative),mas com algumas alterações:

- As etapas 1 a 7 são as mesmas.
- Para a etapa 8, use o seguinte conteúdo de exemplo para o gatilho de solicitação HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  As etapas 9 a 10 são as mesmas.
-  Para as etapas de 11 a 14, use o processo a seguir:

   1. Selecione **+** **Nova etapa** em seguida, escolha, **Adicionar uma condição**. Defina as condições a seguir para que o aplicativo lógico seja executado quando os dados de entrada corresponderem aos valores abaixo.  Ao inserir o valor da versão na caixa de texto, coloque aspas ("0.1.1") para certificar-se de que ele seja avaliado como uma cadeia de caracteres e não um tipo numérico.  O sistema não mostra as aspas se você retorna à página, mas o código subjacente ainda mantém o tipo de cadeia de caracteres.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Condição de carga da Integridade do Serviço"](media/action-groups-logic-app/service-health-payload-condition.png "Condição de carga da Integridade do Serviço")

   1. Na condição **se verdadeiro**, siga as instruções nas etapas de 11 a 13 em [Criar um alerta do registro de atividade ](#create-an-activity-log-alert-administrative) para adicionar a ação da Microsoft Teams.

   1. Defina a mensagem usando uma combinação de HTML e conteúdo dinâmico. Recorte e cole o conteúdo a seguir no campo **Mensagem**. Substitua os campos `[incidentType]`, `[trackingID]`, `[title]`, e `[communication]` pela marcação de conteúdo dinâmica de mesmo nome:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Ação posterior da condição verdadeira da Integridade do Serviço"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Ação posterior da condição verdadeira da Integridade do Serviço")

   1. Para a condição **Se falso** forneça uma mensagem útil:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Ação posterior da condição falsa da Integridade do Serviço"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Ação posterior da condição falsa da Integridade do Serviço")

- A etapa 15 é a mesma. Siga as instruções para salvar seu aplicativo lógico e atualizar seu grupo de ações.

## <a name="create-a-metric-alert"></a>Criar um alerta de métrica

O processo para criar o alerta métrico é semelhante para [criar um alerta de registro de atividade](#create-an-activity-log-alert-administrative),mas com algumas alterações:

- As etapas 1 a 7 são as mesmas.
- Para a etapa 8, use o seguinte conteúdo de exemplo para o gatilho de solicitação HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

- As etapas 9 a 10 são as mesmas.
- Para as etapas de 11 a 14, use o processo a seguir:

  1. Selecione **+** **Nova etapa** em seguida, escolha, **Adicionar uma condição**. Defina as condições a seguir para que o aplicativo lógico seja executado quando os dados de entrada corresponderem aos valores abaixo. Ao inserir o valor da versão na caixa de texto, coloque aspas ("2.0") para certificar-se de que ele seja avaliado como uma cadeia de caracteres e não um tipo numérico.  O sistema não mostra as aspas se você retorna à página, mas o código subjacente ainda mantém o tipo de cadeia de caracteres. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Condição de carga de alerta de métrica"](media/action-groups-logic-app/metric-alert-payload-condition.png "Condição de carga de alerta de métrica")

  1. Na condição **se verdadeiro**, adicione um **Para cada** loop e a ação da Microsoft Teams. Defina a mensagem usando uma combinação de HTML e conteúdo dinâmico.

      !["Ação posterior da condição verdadeira de alerta de métrica"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Ação posterior da condição verdadeira de alerta de métrica")

  1. Na condição **Se falso**, defina uma ação do Microsoft Teams que comunica que o alerta de métrica não corresponde às expectativas do aplicativo lógico e inclua o aplicativo lógico. Inclui o conteúdo JSON. Observe como referenciar o `triggerBody` conteúdo dinâmico na `json()` expressão.

      !["Ação posterior da condição falsa de alerta de métrica"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Ação posterior da condição falsa de alerta de métrica")

- A etapa 15 é a mesma. Siga as instruções para salvar seu aplicativo lógico e atualizar seu grupo de ações.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Chamar outros aplicativos além do Microsoft Teams
Aplicativos Lógicos têm um número de diferentes conectores que permitem disparar ações em um intervalo amplo de aplicativos e bancos de dados. Slack, SQL Server, Oracle, Salesforce, são apenas alguns exemplos. Para obter mais informações sobre conectores, consulte [Conectores do aplicativo lógico](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Próximas etapas
* Obtenha uma [visão geral dos alertas do log de atividades do Microsoft Azure](../../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do Serviço do Azure é publicada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md).

