---
title: Receber alertas do log de atividades nas notificações de serviço do Azure
description: Seja notificado por SMS, email ou webhook quando um serviço do Azure for executado.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75749311"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Criar alertas do log de atividades em notificações de serviço
## <a name="overview"></a>Visão geral

Este artigo mostra como configurar alertas do log de atividades para notificações de integridade do serviço usando o portal do Azure.  

As notificações de integridade do serviço são armazenadas no [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md) , considerando o volume possivelmente grande de informações armazenadas no log de atividades, há uma interface do usuário separada para facilitar a exibição e a configuração de alertas sobre notificações de integridade do serviço. 

Você pode receber um alerta quando o Azure envia notificações de integridade do serviço para sua assinatura do Azure. Você pode configurar o alerta de acordo com:

- A classe de notificação do serviço de integridade (Problemas de serviço, Manutenção planejada, Avisos de integridade).
- A assinatura afetada.
- Os serviços afetados.
- As regiões afetadas.

> [!NOTE]
> As notificações de integridade do serviço não enviam um alerta sobre o recurso de eventos de integridade.

Também é possível configurar para quem o alerta deve ser enviado:

- Selecione um grupo de ações existente.
- Crie um novo grupo de ações (que pode ser usado posteriormente para futuros alertas).

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

Para saber mais sobre como configurar alertas de notificação de integridade do serviço usando modelos do Azure Resource Manager, consulte [modelos do Resource Manager](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Assista a um vídeo sobre como configurar seu primeiro alerta de integridade do serviço do Azure

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Alerta e novo grupo de ações usando o portal do Azure
1. No [portal](https://portal.azure.com), selecione **Integridade do Serviço**.

    ![O serviço “Integridade do Serviço”](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Na seção **Alertas**, selecione **Alertas de integridade**.

    ![A guia “Alertas de integridade”](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selecione **Criar alerta de integridade do serviço** e preencha os campos.

    ![O comando “Criar alerta de integridade do serviço”](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecione a **Assinatura**, os **Serviços** e as **Regiões** sobre os quais você deseja ser alertado.

    ![A caixa de diálogo "Adicionar alerta do log de atividades"](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Esta assinatura é usada para salvar o alerta do log de atividades. O recurso de alerta é implantado para essa assinatura e monitora os eventos no log de atividades para ele.

1. Escolha os **Tipos de evento** sobre os quais você deseja ser alertado: *Problema de serviço*, *Manutenção planejada* e *Consultorias de integridade* 

1. Defina os detalhes do alerta inserindo um **Nome de regra de alerta** e uma **Descrição**.

1. Selecione o **Grupo de recursos** onde você deseja que o alerta seja salvo.

1. Crie um grupo de ação selecionando **Novo grupo de ação**. Insira um nome na caixa **nome do grupo de ações** e insira um nome na caixa **nome curto** . O nome curto é referenciado nas notificações enviadas quando esse alerta é acionado.

    ![Criar um novo grupo de ações](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Defina uma lista de destinatários fornecendo os seguintes itens do destinatário:

    a. **Nome**: insira o nome do destinatário, o alias ou o identificador.

    b. **Tipo de Ação**: selecione SMS, email, webhook, aplicativo do Azure e muito mais.

    c. **Detalhes**: de acordo com o tipo de ação escolhido, insira um número de telefone, endereço de email, URI de webhook, etc.

1. Selecione **OK** para criar o grupo de ação e, em seguida, **Criar regra de alerta** para concluir o alerta.

Em alguns minutos, o alerta estará ativo e começará a disparar com base nas condições especificadas durante a criação.

Saiba como [Configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md). Para saber mais sobre o esquema do webhook para alertas de log de atividades, veja [Webhooks para alertas do log de atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ações definido nessas etapas é reutilizável, como um grupo de ação existente, para todas as definições de alerta futuras.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Alerta com o grupo de ações existente usando o portal do Azure

1. Siga as etapas 1 a 6 na seção anterior para criar sua notificação de integridade do serviço. 

1. Em **Definir grupo de ação**, clique no botão **Selecionar grupo de ação**. Selecione o grupo de ação apropriado.

1. Selecione **Adicionar** para adicionar o grupo de ação e, em seguida, **Criar regra de alerta** para concluir o alerta.

Em alguns minutos, o alerta estará ativo e começará a disparar com base nas condições especificadas durante a criação.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Alerta e novo grupo de ações usando os modelos do Azure Resource Manager

O exemplo a seguir cria um grupo de ações com um destino de email e habilita todas as notificações de integridade de serviço para a assinatura de destino.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
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
            "comments": "Service Health Activity Log Alert",
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
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
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

## <a name="manage-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, ele ficará visível na seção **Alertas** do **Monitor**. Selecione o alerta que você deseja gerenciar:

* Edite-o.
* Exclua-o.
* Desabilite-o ou habilite-o, se desejar interromper temporariamente ou continuar recebendo notificações do alerta.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [as práticas recomendadas para configurar alertas de integridade do serviço do Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Saiba como [configurar notificações por push móvel para a integridade do serviço do Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Saiba mais sobre as [notificações de integridade do serviço](service-notifications.md).
- Saiba mais sobre a [limitação da taxa de notificação](../azure-monitor/platform/alerts-rate-limiting.md).
- Examine o [esquema de webhook de alerta do log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas do log de atividades](../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.
- Saiba mais sobre [grupos de ações](../azure-monitor/platform/action-groups.md).
