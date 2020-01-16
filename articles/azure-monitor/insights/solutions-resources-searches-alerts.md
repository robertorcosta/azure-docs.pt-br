---
title: Pesquisas salvas em soluções de gerenciamento | Microsoft Docs
description: As soluções de gerenciamento normalmente incluem consultas de log salvas para analisar os dados coletados pela solução. Este artigo descreve como definir Log Analytics pesquisas salvas em um modelo do Resource Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ff9c45ffb636f53951a763f617c25a2e8c09088
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977732"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Adicionar alertas e pesquisas salvas do Log Analytics à solução de gerenciamento (versão prévia)

> [!IMPORTANT]
> Conforme [anunciado anteriormente](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), os espaços de trabalho do log Analytics criados após *1º de junho de 2019* – poderão gerenciar regras de alerta usando **apenas** a [API REST](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)do Azure scheduledQueryRules, o modelo de [Azure Resource Manager](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) e o cmdlet do [PowerShell](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Os clientes podem facilmente [alternar seus meios preferenciais de gerenciamento de regras de alerta](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) para espaços de trabalho mais antigos para aproveitar Azure monitor scheduledQueryRules como padrão e obter muitos [novos benefícios](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) como a capacidade de usar cmdlets nativos do PowerShell, um período de tempo de lookback maior em regras, a criação de regras em um grupo de recursos ou assinatura separado e muito mais.

> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento que estão atualmente em versão prévia. Os esquemas descritos a seguir estão sujeitos a alterações.

As [Soluções de gerenciamento](solutions.md) geralmente incluirão [pesquisas salvas](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics para analisar os dados coletados pela solução. Elas também podem definir [alertas](../../azure-monitor/platform/alerts-overview.md) para notificar o usuário ou executar automaticamente a ação em resposta a um problema crítico. Este artigo descreve como definir a Log Analytics pesquisas salvas e alertas em um [modelo do Resource Manager](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) para que eles possam ser incluídos em [soluções de gerenciamento de](solutions-creating.md).

> [!NOTE]
> As amostras deste artigo usam variáveis e parâmetros que são necessários ou comuns a soluções de gerenciamento e descritos em [Projetar e criar uma solução de gerenciamento no Azure](solutions-creating.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já está familiarizado com o modo para [criar uma solução de gerenciamento](solutions-creating.md) e com a estrutura de um [modelo do Resource Manager](../../azure-resource-manager/templates/template-syntax.md) e de um arquivo de solução.


## <a name="log-analytics-workspace"></a>Espaço de Trabalho do Log Analytics
Todos os recursos de Log Analytics estão contidos em um [workspace](../../azure-monitor/platform/manage-access.md). Como descrito no [espaço de trabalho do Log Analytics e conta de Automação](solutions.md#log-analytics-workspace-and-automation-account), o espaço de trabalho não está incluído na solução de gerenciamento, mas deverá existir antes que a solução seja instalada. Se ela não estiver disponível, a instalação da solução falhará.

O nome do workspace é no nome de cada recurso de Log Analytics. Isso é feito na solução com o parâmetro **workspace**, conforme descrito no exemplo a seguir de um recurso SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Versão da API do Log Analytics
Todos os recursos do Log Analytics definidos em um modelo do Resource Manager têm uma propriedade **apiVersion** que define a versão da API que o recurso deve usar.

A tabela a seguir lista a versão de API para o recurso usado neste exemplo.

| Tipo de recurso | Versão da API | Consulta |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>Pesquisas salvas
Incluir [pesquisas salvas](../../azure-monitor/log-query/log-query-overview.md) em uma solução para permitir aos usuários consultar dados coletados pela solução. As pesquisas salvas aparecem em **Pesquisas salvas** no portal do Azure. Uma pesquisa salva também é necessária para cada alerta.

Os recursos [da pesquisa salva do Log Analytics](../../azure-monitor/log-query/log-query-overview.md) têm um tipo `Microsoft.OperationalInsights/workspaces/savedSearches` e a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Todas as propriedades de uma pesquisa salva são descritas na tabela a seguir.

| Propriedade | Description |
|:--- |:--- |
| category | A categoria para a pesquisa salva.  As pesquisas salvas na mesma solução geralmente compartilham uma única categoria para que eles são agrupados juntos no console. |
| displayname | Nome para exibição para a pesquisa salva no portal. |
| Consulta | Consulta a executar. |

> [!NOTE]
> Você talvez precise usar caracteres de escape na consulta, se ele inclui os caracteres que podem ser interpretados como JSON. Por exemplo, se a consulta fosse **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"** , ela deveria ser gravada no arquivo de solução como **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"** .

## <a name="alerts"></a>Alertas
Os [Alertas de Log do Azure](../../azure-monitor/platform/alerts-unified-log.md) são criados por regras de Alerta do Azure que executam consultas de log especificadas em intervalos regulares. Se os resultados da consulta correspondência aos critérios especificados, será criado um registro de alerta e uma ou mais ações são executadas usando [Grupos de Ação](../../azure-monitor/platform/action-groups.md).

As ações dos usuários que estendem os alertas para o Azure agora são controladas em grupos de ações do Azure. Quando um workspace e seus alertas são estendidos para o Azure, será possível recuperar ou adicionar ações usando o [Grupo de Ação – Modelo do Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
As regras de alerta na solução de gerenciamento herdado são formadas pelos três recursos diferentes a seguir.

- **Pesquisa salva.** Define a pesquisa de logs executada. Várias regras de alerta podem compartilhar uma única pesquisa salva.
- **Agenda.** Define a frequência com que a pesquisa de logs é executada. Cada regra de alerta tem apenas um agendamento.
- **Ação de alerta.** Cada regra de alerta tem um recurso de ação ou grupo de recurso de ação (herdado) com um tipo de **Alerta** que define os detalhes do alerta, como os critérios para quando um registro de alerta é criado e a gravidade do alerta. O recurso [Grupo de ação](../../azure-monitor/platform/action-groups.md) pode ter uma lista de ações configuradas a serem tomadas quando o alerta é disparado, como chamada de voz, SMS, email, webhook ferramenta ITSM, runbook de automação, aplicativo lógico, etc.

Salvar pesquisa recursos descritos acima. Outros recursos são descritos abaixo.

### <a name="schedule-resource"></a>Recursos de agendamento

Uma pesquisa salva pode ter uma ou mais agendas com cada agenda que representa uma regra de alerta separada. A agenda define a frequência com que a pesquisa é executada e o intervalo de tempo em que os dados são recuperados. Os recursos de agendamento têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` e ter a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
As propriedades de recursos de agendamento são descritas na tabela a seguir.

| Nome do elemento | Obrigatório | Description |
|:--|:--|:--|
| Habilitado       | Sim | Especifica se o alerta está habilitado quando ele é criado. |
| intervalo      | Sim | A frequência com a consulta é executada em minutos. |
| queryTimeSpan | Sim | Período de tempo em minutos no qual avaliar resultados. |

O recurso de agendamento deve depender a pesquisa salva para que ele seja criado antes da agenda.
> [!NOTE]
> O Nome do Agendamento precisa ser exclusivo no workspace em questão; dois agendamentos não podem ter a mesma ID, mesmo se forem associados a diferentes pesquisas salvas. Além disso, todas as pesquisas, agendas e ações salvas criadas com a API do Log Analytics deve estar em minúsculas.

### <a name="actions"></a>Ações
Um agendamento pode ter várias ações. Uma ação pode definir um ou mais processos a serem executados, como enviar um email ou iniciar um runbook, ou então ela pode definir um limite que determina quando os resultados de uma pesquisa correspondem a certos critérios. Algumas ações definirão ambos para que os processos sejam executados quando o limite for atingido.
As ações podem ser definidas usando o recurso de [grupo de ações] ou recurso de ação.

Há dois tipos de recurso de ação especificado pelo **tipo** propriedade. Um agendamento requer uma ação **Alerta**, que define os detalhes da regra de alerta e quais ações são tomadas quando um alerta é criado. Recursos de ação com um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

Ações de alerta tem a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

As propriedades de Recursos de ação de alerta são descritas nas tabelas a seguir.

| Nome do elemento | Obrigatório | Description |
|:--|:--|:--|
| `type` | Sim | Tipo da ação.  Isso será **Alerta** para ações de alerta. |
| `name` | Sim | Nome de exibição para o alerta.  Esse é o nome que é exibido no console para a regra de alerta. |
| `description` | Não | Descrição opcional do alerta. |
| `severity` | Sim | Severidade do alerta registro dos seguintes valores:<br><br> **crítico**<br>**aviso**<br>**informativo**

#### <a name="threshold"></a>Limite
Esta seção é necessária. Define as propriedades para o limite de alerta.

| Nome do elemento | Obrigatório | Description |
|:--|:--|:--|
| `Operator` | Sim | O operador para a comparação dos seguintes valores:<br><br>**gt = maior que<br>lt = menor que** |
| `Value` | Sim | O valor para comparar os resultados. |

##### <a name="metricstrigger"></a>MetricsTrigger
Esta seção é opcional. Inclua-o para um alerta de métrica de medição.

| Nome do elemento | Obrigatório | Description |
|:--|:--|:--|
| `TriggerCondition` | Sim | Especifica se o limite do número total de violações ou falhas consecutivas dos seguintes valores:<br><br>**Total<br>consecutivas** |
| `Operator` | Sim | O operador para a comparação dos seguintes valores:<br><br>**gt = maior que<br>lt = menor que** |
| `Value` | Sim | Número de vezes que os critérios devem ser atendidos para disparar o alerta. |


#### <a name="throttling"></a>Limitação
Esta seção é opcional. Inclua esta seção se você desejar Suprimir alertas da mesma regra por algum tempo depois que um alerta é criado.

| Nome do elemento | Obrigatório | Description |
|:--|:--|:--|
| DurationInMinutes | Sim, se a limitação elemento incluído | Número de minutos para suprimir alertas depois da mesma regra de alerta será criado. |

#### <a name="azure-action-group"></a>Grupo de ações do Azure
Todos os alertas no Azure, use o Grupo de Ações como mecanismo padrão para lidar com ações. Com o Grupo de Ações, você pode especificar suas ações uma vez e, em seguida, associar o grupo de ações para vários alertas - em todo o Azure. Sem a necessidade de declarar repetidamente as mesmas ações. Os Grupos de Ações dão suportam a várias ações - incluindo Email, SMS, Chamada de voz, Conexão ITSM, Runbook de automação, URI de Webhook e muito mais.

O usuário que tiver estendido seus alertas ao Azure tem uma agenda que deve ter agora detalhes do Grupo de Ações passado junto com o limite, para poder criar um alerta. Detalhes de email, URLs de Webhook, detalhes de automação de runbook e outras ações precisam ser definidas no Grupo de Ações antes de criar um alerta; é possível criar o [Grupo de Ações do Azure Monitor](../../azure-monitor/platform/action-groups.md) no Portal ou usar [Grupo de Ações – Modelo de Recursos](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Nome do elemento | Obrigatório | Description |
|:--|:--|:--|
| AzNsNotification | Sim | A ID de recurso do grupo de ações do Azure a ser associada ao alerta para tomar as ações necessárias quando os critérios de alerta são atendidos. |
| CustomEmailSubject | Não | A linha do assunto personalizada do email enviado para todos os endereços especificados no grupo de ação associado. |
| CustomWebhookPayload | Não | Conteúdo personalizado a ser enviado a todos os pontos de extremidade de webhook definidos no grupo de ações associado. O formato depende do que está o webhook está esperando e deve ser um JSON válido serializado. |

## <a name="sample"></a>Amostra

A seguir está um exemplo de uma solução que inclui os seguintes recursos:

- Pesquisa salva
- Agenda
- Grupo de ações

O exemplo usa [parâmetros de solução padrão]( solutions-solution-file.md#parameters) variáveis que normalmente seriam usados em uma solução em vez de embutir valores nas definições de recurso.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

O arquivo de parâmetro a seguir fornece valores de amostras para esta solução.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Próximos passos
* [Adicionar exibições](solutions-resources-views.md) à sua solução de gerenciamento.
* [Adicionar runbooks de automação e outros recursos](solutions-resources-automation.md) à sua solução de gerenciamento.
