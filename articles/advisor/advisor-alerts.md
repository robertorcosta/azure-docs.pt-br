---
title: Crie alertas do Azure Advisor para novas recomendações
description: Crie alertas do Azure Advisor para nova recomendação
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443166"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Crie alertas do Azure Advisor sobre novas recomendações 

Este artigo mostra como configurar um alerta para novas recomendações do Azure Advisor usando os modelos do portal Azure e do Azure Resource Manager. 

Sempre que o Azure Advisor detecta uma nova recomendação para um de seus recursos, um evento é armazenado no [registro de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview). Você pode configurar alertas para esses eventos do Azure Advisor usando uma experiência de criação de alertas específicos de recomendação. Você pode selecionar uma assinatura e, opcionalmente, um grupo de recursos para especificar os recursos que deseja receber alertas. 

Você também pode determinar os tipos de recomendações usando essas propriedades:

* Categoria
* Nível de impacto
* Tipo de recomendação

Você também pode configurar a ação que ocorrerá quando um alerta é acionado por:  

* Selecionando um grupo de ação existente
* Criando um novo grupo de ação

Para saber mais sobre grupos de ação, consulte [Criar e gerenciar grupos de ação](../azure-monitor/platform/action-groups.md).

> [!NOTE] 
> Atualmente, os alertas de conselheiro estão disponíveis apenas para recomendações de alta disponibilidade, desempenho e custo. Recomendações de segurança não são suportadas. 

## <a name="in-the-azure-portal"></a>No portal do Azure
1. No **portal,** selecione **Azure Advisor**.

    ![Azure Advisor em portal](./media/advisor-alerts/create1.png)

2. Na seção **Monitoramento** do menu esquerdo, selecione **Alertas**. 

    ![Alertas no Advisor](./media/advisor-alerts/create2.png)

3. Selecione **novo alerta de conselheiro**.

    ![Novo Alerta de Conselheiro](./media/advisor-alerts/create3.png)

4. Na seção **Escopo,** selecione a assinatura e, opcionalmente, o grupo de recursos no que deseja ser alertado. 

    ![Escopo de alerta do advisor](./media/advisor-alerts/create4.png)

5. Na seção **Condição,** selecione o método que deseja usar para configurar seu alerta. Se você quiser alertar para todas as recomendações para uma determinada categoria e/ou nível de impacto, selecione **Categoria e nível de impacto**. Se você quiser alertar para todas as recomendações de um determinado tipo, selecione **Tipo de Recomendação**.

    ![Condição de alerta do Azure Advisor](./media/advisor-alerts/create5.png)

6. Dependendo da opção Configurar por opção selecionada, você poderá especificar os critérios. Se você quiser todas as recomendações, basta deixar os campos restantes em branco. 

    ![Grupo de ação de alerta de conselheiro](./media/advisor-alerts/create6.png)

7. Na seção **grupos de ação,** selecione **Adicionar existente** para usar um grupo de ação que você já criou ou selecione **Criar novo** para configurar um novo grupo [de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). 

    ![Alerta de conselheiro adicionar existente](./media/advisor-alerts/create7.png)

8. Na seção Detalhes alerta, dê ao seu alerta um nome e uma breve descrição. Se você quiser que seu alerta seja ativado, deixe **Habilitar a regra na** seleção de criação definida **como Sim**. Em seguida, selecione o grupo de recursos para salvar seu alerta. Isso não afetará o escopo de segmentação da recomendação. 

    ![Azure Advisor Banner](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Com um modelo de Gerenciador de recursos do Azure

Este modelo de Gerenciador de recursos cria um alerta de recomendação e um novo grupo de ação.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Configure alertas de recomendação para usar um webhook
Esta seção mostra como configurar alertas do Azure Advisor para enviar dados de recomendação através de webhooks para seus sistemas existentes. 

Você pode configurar alertas para ser notificado quando tiver uma nova recomendação do Advisor sobre um de seus recursos. Esses alertas podem notificá-lo por e-mail ou mensagem de texto, mas também podem ser usados para se integrar com seus sistemas existentes através de um webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Usando a carga de alerta de recomendação do Advisor
Se você quiser integrar alertas do Advisor em seus próprios sistemas usando um webhook, você precisará analisar a carga json enviada a partir da notificação. 

Quando você configura seu grupo de ação para este alerta, você seleciona se deseja usar o esquema de alerta comum. Se você selecionar o esquema de alerta comum, sua carga será como: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Se você não usar o esquema comum, sua carga será a seguinte: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

Em ambos os esquemas, você pode identificar eventos `Recommendation` de recomendação do Advisor procurando por **eventosFonte** é e **operaçãoNome** é `Microsoft.Advisor/recommendations/available/action`.

Alguns dos outros campos importantes que você pode querer usar são: 

* *alertaTargetIDs* (no esquema comum) ou *resourceId* (esquema legado)
* *recomendaçãoType*
* *recomendaçãoNome*
* *recomendaçãoCategoria*
* *recomendaçãoImpacto*
* *recomendaçãoResourceLink*


## <a name="manage-your-alerts"></a>Gerenciar seus alertas 

A partir do Azure Advisor, você pode editar, excluir ou desativar e ativar seus alertas de recomendações. 

1. No **portal,** selecione **Azure Advisor**.

    ![Azure Advisor Banner](./media/advisor-alerts/create1.png)

2. Na seção **Monitoramento** do menu esquerdo, selecione **Alertas**.

    ![Azure Advisor Banner](./media/advisor-alerts/create2.png)

3. Para editar um alerta, clique no nome Alerta para abrir o alerta e editar os campos que deseja editar.

4. Para excluir, ativar ou desativar um alerta, clique na elipse no final da linha e selecione a ação que deseja tomar.
 

