---
title: Criar uma configuração de diagnóstico no Azure usando o modelo do Resource Manager
description: Crie configurações de diagnóstico usando um modelo do Gerenciador de recursos para encaminhar logs da plataforma Azure para logs do Monitor Do Azure, armazenamento Azure ou Hubs de Eventos Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672422"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Criar configuração de diagnóstico no Azure usando um modelo de Gerenciador de recursos
[As configurações de diagnóstico](diagnostic-settings.md) no Azure Monitor especificam para onde enviar [registros da plataforma coletados](platform-logs-overview.md) pelos recursos do Azure e da plataforma Azure da qual dependem. Este artigo fornece detalhes e exemplos para usar um [modelo do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) para criar e configurar configurações de diagnóstico para coletar logs de plataforma para diferentes destinos.

> [!NOTE]
> Como você não pode [criar uma configuração de diagnóstico](diagnostic-settings.md) para o registro de atividadedo Azure usando o PowerShell ou a CLI, como configurações de diagnóstico para outros recursos do Azure, crie um modelo de Gerenciador de recursos para o registro de atividades usando as informações deste artigo e implante o modelo usando PowerShell ou CLI.

## <a name="deployment-methods"></a>Métodos de implantação
Você pode implantar modelos do Gerenciador de recursos usando qualquer método válido, incluindo PowerShell e CLI. As configurações de diagnóstico para registro `az deployment create` de `New-AzDeployment` atividade devem ser implantadas em uma assinatura usando para CLI ou para PowerShell. As configurações de diagnóstico para logs `az group deployment create` de recursos `New-AzResourceGroupDeployment` devem ser implantadas em um grupo de recursos usando para CLI ou para PowerShell.

Consulte [Implantar recursos com modelos de gerenciador de recursos e recursos do Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) e implantar recursos com [modelos de gerenciador de recursos e CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md) para obter detalhes. 





## <a name="resource-logs"></a>Logs de recursos
Para logs de recursos, `<resource namespace>/providers/diagnosticSettings` adicione um recurso de tipo ao modelo. A seção propriedades segue o formato descrito em [Configurações de Diagnóstico - Criar ou Atualizar](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Forneça `category` um `logs` na seção para cada uma das categorias válidas para o recurso que você deseja coletar. Adicione `metrics` a propriedade para coletar métricas de recursos para os mesmos destinos se o [recurso suportar métricas](metrics-supported.md).

A seguir está um modelo que coleta uma categoria de log de recursos para um recurso específico para um espaço de trabalho, conta de armazenamento e hub de eventos do Log Analytics.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Exemplo
A seguir, um exemplo que cria uma configuração de diagnóstico para uma configuração de escala automática que permite o streaming de logs de recursos para um hub de eventos, uma conta de armazenamento e um espaço de trabalho do Log Analytics.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Log de atividades
Para o registro de atividades do `Microsoft.Insights/diagnosticSettings`Azure, adicione um recurso do tipo . As categorias disponíveis estão [listadas em Categorias no Registro de Atividades](activity-log-view.md#categories-in-the-activity-log). A seguir está um modelo que coleta todas as categorias de registro de atividades para um espaço de trabalho do Log Analytics, conta de armazenamento e hub de eventos.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre [os logs da plataforma no Azure](platform-logs-overview.md).
* Saiba mais sobre [as configurações de diagnóstico](diagnostic-settings.md).
