---
title: Atualizar aplicativos lógicos & runbooks para migração de alertas
description: Aprenda a modificar seus webhooks, aplicativos lógicos e runbooks para se preparar para migração voluntária.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: f31fcc07bed0287c2f86ca4fe52bf02a2a1d2a71
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114417"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Preparar seus aplicativos lógicos e runbooks para a migração de regras de alerta clássicos

Como [anunciado anteriormente,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão sendo retirados em setembro de 2019 (originalmente julho de 2019). Uma ferramenta de migração está disponível no portal Azure para clientes que usam regras clássicas de alerta e que querem acionar a migração.

> [!NOTE]
> Devido ao atraso na implantação da ferramenta de migração, a data de aposentadoria para migração de alertas clássicos foi prorrogada para 31 de agosto de 2019 a partir da data originalmente anunciada de 30 de junho de 2019.

Se você optar por migrar voluntariamente suas regras clássicas de alerta para novas regras de alerta, esteja ciente de que existem algumas diferenças entre os dois sistemas. Este artigo explica essas diferenças e como você pode se preparar para a mudança.

## <a name="api-changes"></a>Alterações de API

As APIs que criam e`microsoft.insights/alertrules`gerenciam regras clássicas de alerta são diferentes`microsoft.insights/metricalerts`das APIs que criam e gerenciam novos alertas métricos ( ). Se você criar e gerenciar programáticamente regras clássicas de alerta hoje, atualize seus scripts de implantação para trabalhar com as novas APIs.

A tabela a seguir é uma referência às interfaces programáticas para alertas clássicos e novos:

|         |Alertas clássicos  |Novos alertas métricos |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|CLI do Azure     | [alerta de monitor az](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [alerta de métricas do monitor az](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Modelo do Azure Resource Manager | [Para alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Para novos alertas métricos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Alterações de carga de notificação

O formato de carga de notificação é ligeiramente diferente entre [regras clássicas de alerta](alerts-webhooks.md) e novos [alertas métricos.](alerts-metric-near-real-time.md#payload-schema) Se você tiver qualquer webhook, aplicativo lógico ou ações de runbook que sejam acionadas por regras clássicas de alerta, você deve atualizar esses pontos finais de notificação para aceitar o formato de carga de novos alertas métricos.

Use a tabela a seguir para mapear os campos de carga do webhook do formato clássico para o novo formato:

|  |Alertas clássicos  |Novos alertas métricos |
|---------|---------|---------|
|O alerta foi ativado ou resolvido?    | **status**       | **data.status** |
|Informações contextuais sobre o alerta     | **contexto**        | **data.context**        |
|Carimbo de tempo no qual o alerta foi ativado ou resolvido     | **context.timestamp**       | **data.context.timestamp**        |
| ID da regra de alerta | **context.id** | **data.context.id** |
| Nome da regra de alerta | **context.name** | **data.context.name** |
| Descrição da regra de alerta | **context.description** | **data.context.description** |
| Condição da regra de alerta | **context.condition** | **data.context.condition** |
| Nome da métrica | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Agregação de tempo (como a métrica é agregada sobre a janela de avaliação)| **context.condition.timeAggregation** | **context.condition.timeAggregation** |
| Período de avaliação | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operador (como o valor métrico agregado é comparado com o limiar) | **context.condition.operator** | **data.context.condition.operator** |
| Limite | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Valor métrico | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| ID da assinatura | **context.subscriptionId** | **data.context.subscriptionId** |
| Grupo de recursos do recurso afetado | **context.resourceGroup** | **data.context.resourceGroup** |
| Nome do recurso afetado | **context.resourceName** | **data.context.resourceNome** |
| Tipo do recurso afetado | **context.resourceType** | **data.context.resourceType** |
| ID de recursos do recurso afetado | **context.resourceId** | **data.context.resourceId** |
| Link direto para a página de resumo de recursos do portal | **context.portalLink** | **data.context.portalLink** |
| Campos de carga personalizados a serem passados para o webhook ou aplicativo lógico | **Propriedades** | **data.propriedades** |

As cargas são semelhantes, como você pode ver. A seguinte seção oferece:

- Detalhes sobre a modificação de aplicativos lógicos para trabalhar com o novo formato.
- Um exemplo de runbook que analisa a carga de notificação para novos alertas.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modifique um aplicativo lógico para receber uma notificação de alerta métrica

Se você estiver usando aplicativos lógicos com alertas clássicos, você deve modificar seu código de aplicativo lógico para analisar a nova carga de alertas métricos. Siga estas etapas:

1. Crie um novo aplicativo lógico.

1. Use o modelo "Azure Monitor - Metrics Alert Handler". Este modelo tem um gatilho **de solicitação HTTP** com o esquema apropriado definido.

    ![modelo de aplicativo de lógica](media/alerts-migration/logic-app-template.png "Modelo de alerta métrico")

1. Adicione uma ação para hospedar sua lógica de processamento.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Use um manual de auto-ia que recebe uma notificação de alerta métrica

O exemplo a seguir fornece código PowerShell para usar em seu livro de execução. Este código pode analisar as cargas para regras clássicas de alerta métrico e novas regras de alerta métrica.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Para obter um exemplo completo de um runbook que pára uma máquina virtual quando um alerta é acionado, consulte a [documentação do Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integração de parceiros via webhooks

A maioria dos [nossos parceiros que se integram com alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/partners) já suportam alertas métricos mais novos através de suas integrações. Integrações conhecidas que já funcionam com novos alertas métricos são:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se você estiver usando uma integração de parceiro sem lista aqui, confirme com o provedor de integração que a integração funciona com novos alertas métricos.

## <a name="next-steps"></a>Próximas etapas

- [Como usar a ferramenta de migração](alerts-using-migration-tool.md)
- [Entender como a ferramenta de migração funciona](alerts-understand-migration.md)
