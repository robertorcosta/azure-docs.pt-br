---
title: Criar alertas do supervisor do Azure para novas recomendações usando o modelo do Resource Manager
description: Criar alertas do supervisor do Azure para nova recomendação
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: ef15891cc01d0481c6253023de262f14dce0ec81
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921066"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Início rápido: criar alertas do supervisor do Azure sobre novas recomendações usando um modelo do ARM

Este artigo mostra como configurar um alerta para novas recomendações do Azure Advisor usando um modelo de Azure Resource Manager (modelo ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Sempre que o Azure Advisor detecta uma nova recomendação para um de seus recursos, um evento é armazenado no [log de atividades do Azure](/azure/azure-monitor/platform/activity-logs-overview). Você pode configurar alertas para esses eventos do Azure Advisor usando uma experiência de criação de alertas específica de recomendação. Você pode selecionar uma assinatura e, opcionalmente, um grupo de recursos para especificar os recursos nos quais deseja receber alertas.

Você também pode determinar os tipos de recomendações usando essas propriedades:

- Categoria
- Nível de impacto
- Tipo de recomendação

Você também pode configurar a ação que ocorrerá quando um alerta for disparado por:  

- Selecionando um grupo de ação existente
- Criando um novo grupo de ação

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Atualmente, os alertas do Advisor estão disponíveis apenas para alta disponibilidade, desempenho e recomendações de custo. Não há suporte para recomendações de segurança.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Para executar os comandos do computador local, instale CLI do Azure ou os módulos Azure PowerShell. Para obter mais informações, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli) e [instalar Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Examinar o modelo

O modelo a seguir cria um grupo de ações com um destino de email e habilita todas as notificações de integridade do serviço para a assinatura de destino. Salve este modelo como *CreateAdvisorAlert.jsem*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
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
      "apiVersion": "2019-06-01",
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

O modelo define dois recursos:

- [Microsoft. insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Implantar o modelo

Implante o modelo usando qualquer método padrão para [implantar um modelo ARM](../azure-resource-manager/templates/deploy-portal.md) , como os exemplos a seguir, usando a CLI e o PowerShell. Substitua os valores de exemplo para o **grupo de recursos**e o **EmailAddress** pelos valores apropriados para seu ambiente. O nome do espaço de trabalho deve ser exclusivo entre todas as assinaturas do Azure.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Validar a implantação

Verifique se o espaço de trabalho foi criado usando um dos comandos a seguir. Substitua os valores de exemplo do **grupo de recursos** pelo valor usado acima.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar trabalhando com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão. Quando não for mais necessário, exclua o grupo de recursos, que exclui a regra de alerta e os recursos relacionados. Para excluir o grupo de recursos usando CLI do Azure ou Azure PowerShell

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Próximas etapas

- Obtenha uma [visão geral dos alertas do log de atividades](../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.
- Saiba mais sobre [grupos de ação](../azure-monitor/platform/action-groups.md).
