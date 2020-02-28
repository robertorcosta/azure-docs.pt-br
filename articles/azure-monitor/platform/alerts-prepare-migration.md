---
title: Prepare-se para Azure Monitor migração de alertas clássicos atualizando seus aplicativos lógicos e runbooks
description: Saiba como modificar seus WebHooks, aplicativos lógicos e runbooks para se preparar para a migração voluntária.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 9219e105acb98424939030af76b526d475585619
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665585"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Preparar seus aplicativos lógicos e runbooks para a migração de regras de alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), os alertas clássicos no Azure monitor estão sendo desativados em setembro de 2019 (foi originalmente 2019 de julho). Uma ferramenta de migração está disponível no portal do Azure aos clientes que usam regras de alerta clássicas e que desejam disparar a migração por conta própria.

> [!NOTE]
> Devido ao atraso na distribuição da ferramenta de migração, a data de desativação da migração de alertas clássicos foi prorrogada para 31 de agosto de 2019 da data de lançamento original de 30 de junho de 2019.

Se você optar por migrar voluntariamente suas regras de alerta clássicas para novas regras de alerta, lembre-se de que há algumas diferenças entre os dois sistemas. Este artigo explica essas diferenças e como você pode se preparar para a alteração.

## <a name="api-changes"></a>Alterações de API

As APIs que criam e gerenciam as regras de alerta clássicas (`microsoft.insights/alertrules`) são diferentes das APIs que criam e gerenciam novos alertas de métrica (`microsoft.insights/metricalerts`). Se você criar e gerenciar programaticamente regras de alerta clássicas hoje, atualize seus scripts de implantação para trabalhar com as novas APIs.

A tabela a seguir é uma referência às interfaces programáticas para alertas clássicos e novos:

|         |Alertas clássicos  |Novos alertas de métrica |
|---------|---------|---------|
|API REST     | [Microsoft. insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [Microsoft. insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|CLI do Azure     | [alerta de monitor AZ](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [alerta AZ monitor de métricas](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Modelo do Azure Resource Manager | [Para alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Para novos alertas de métrica](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Alterações de carga de notificação

O formato da carga de notificação é ligeiramente diferente entre [as regras de alerta clássicas](alerts-webhooks.md) e os [novos alertas de métrica](alerts-metric-near-real-time.md#payload-schema). Se você tiver qualquer webhook, aplicativo lógico ou ações de runbook disparadas por regras de alerta clássicas, você deverá atualizar esses pontos de extremidade de notificação para aceitar o formato de carga de novos alertas de métrica.

Use a tabela a seguir para mapear os campos de carga do webhook do formato clássico para o novo formato:

|  |Alertas clássicos  |Novos alertas de métrica |
|---------|---------|---------|
|O alerta foi ativado ou resolvido?    | **status**       | **Data. status** |
|Informações contextuais sobre o alerta     | **context**        | **Data. Context**        |
|Carimbo de data/hora em que o alerta foi ativado ou resolvido     | **contexto. Timestamp**       | **Data. Context. Timestamp**        |
| ID da regra de alerta | **context.id** | **data.context.id** |
| Nome da regra de alerta | **context.name** | **data.context.name** |
| Descrição da regra de alerta | **contexto. Descrição** | **Data. Context. Description** |
| Condição de regra de alerta | **contexto. condição** | **Data. Context. Condition** |
| Nome da métrica | **Context. condição. metricname** | **Data. Context. Condition. allOf [0]. metricname** |
| Agregação de tempo (como a métrica é agregada na janela de avaliação)| **contexto. condição. timeaggregation** | **contexto. condição. timeaggregation** |
| Período de avaliação | **Context. condição. windowSize** | **Data. Context. Condition. windowSize** |
| Operador (como o valor da métrica agregada é comparado com o limite) | **Context. Condition. Operator** | **Data. Context. Condition. Operator** |
| Limite | **contexto. condição. limite** | **Data. Context. Condition. allOf [0]. Threshold** |
| Valor da métrica | **contexto. condição. metricvalue** | **Data. Context. Condition. allOf [0]. metricvalue** |
| ID da assinatura | **contexto. SubscriptionId** | **Data. Context. SubscriptionId** |
| Grupo de recursos do recurso afetado | **Context. resourcegroup** | **Data. Context. resourcegroup** |
| Nome do recurso afetado | **Context. resourceName** | **Data. Context. resourceName** |
| Tipo do recurso afetado | **Context. resourceType** | **Data. Context. resourceType** |
| ID de recurso do recurso afetado | **Context. ResourceId** | **Data. Context. ResourceId** |
| Link direto para a página de Resumo de recursos do portal | **Context. portalLink** | **Data. Context. portalLink** |
| Campos de carga personalizada a serem passados para o webhook ou o aplicativo lógico | **properties** | **Data. Properties** |

As cargas são semelhantes, como você pode ver. A seção a seguir oferece:

- Detalhes sobre como modificar aplicativos lógicos para trabalhar com o novo formato.
- Um exemplo de runbook que analisa a carga de notificação para novos alertas.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modificar um aplicativo lógico para receber uma notificação de alerta de métrica

Se você estiver usando aplicativos lógicos com alertas clássicos, deverá modificar o código do aplicativo lógico para analisar a nova carga de alertas de métrica. Siga estas etapas:

1. Crie um novo aplicativo lógico.

1. Use o modelo "manipulador de alerta de métricas de Azure Monitor". Este modelo tem um gatilho de **solicitação HTTP** com o esquema apropriado definido.

    ![lógica-aplicativo-modelo](media/alerts-migration/logic-app-template.png "Modelo de alerta de métrica")

1. Adicione uma ação para hospedar sua lógica de processamento.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Usar um runbook de automação que recebe uma notificação de alerta de métrica

O exemplo a seguir fornece o código do PowerShell para usar em seu runbook. Esse código pode analisar as cargas para as regras de alerta de métrica clássica e novas regras de alerta de métrica.

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

Para obter um exemplo completo de um runbook que para uma máquina virtual quando um alerta é disparado, consulte a [documentação da automação do Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integração de parceiros por meio de WebHooks

A maioria de [nossos parceiros que se integram com alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/partners) já dão suporte a alertas de métrica mais recentes por meio de suas integrações. As integrações conhecidas que já funcionam com novos alertas de métrica são:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se você estiver usando uma integração de parceiro que não está listada aqui, confirme com o provedor de integração que a integração funciona com novos alertas de métrica.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Como usar a ferramenta de migração](alerts-using-migration-tool.md)
- [Entender como a ferramenta de migração funciona](alerts-understand-migration.md)
