---
title: Exibir logs de atividades para alterações de RBAC para recursos do Azure | Microsoft Docs
description: Exibir logs de atividades para alterações de RBAC (controle de acesso baseado em função) para recursos do Azure nos últimos 90 dias.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 753c626fe44193b83cbd992f225fe01c2ff67f89
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75744813"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Exibir logs de atividades para alterações de RBAC para recursos do Azure

Às vezes, você precisa de informações sobre alterações de RBAC (controle de acesso baseado em função) para recursos do Azure, como para fins de auditoria ou solução de problemas. Sempre que alguém faz alterações em definições de função ou definições de função dentro de suas assinaturas, as alterações são registradas no [Log de Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md). Você pode exibir os logs de atividade para ver todas as alterações de RBAC dos últimos 90 dias.

## <a name="operations-that-are-logged"></a>Operações registradas em log

Aqui estão as operações relacionadas à RBAC que são registradas no Activity Log:

- Criar atribuição de função
- Excluir atribuição de função
- Criar ou atualizar definição de função personalizada
- Excluir definição de função personalizada

## <a name="azure-portal"></a>Portal do Azure

A maneira mais fácil para começar é exibir os logs de atividade com o portal do Azure. A captura de tela a seguir mostra um exemplo de um log de atividades que foi filtrado para exibir operações de atribuição de função e definição de função. Ele também inclui um link para baixar os logs como um arquivo CSV.

![Logs de atividade usando o portal – captura de tela](./media/change-history-report/activity-log-portal.png)

O log de atividades no portal tem vários filtros. Estes são os filtros de RBAC:

|Filtrar  |Valor  |
|---------|---------|
|Categoria de evento     | <ul><li>Administrativo</li></ul>         |
|Operação     | <ul><li>Criar atribuição de função</li> <li>Excluir atribuição de função</li> <li>Criar ou atualizar definição de função personalizada</li> <li>Excluir definição de função personalizada</li></ul>      |


Para saber mais sobre logs de atividades, confira [Exibir eventos no log de atividades](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Para exibir logs de atividade com o Azure PowerShell, use o comando [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog).

Esse comando abaixo lista todas as alterações de atribuição de função em uma assinatura nos os últimos sete dias:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Esse comando abaixo lista todas as definição de atribuição de função em um grupo de recursos nos os últimos sete dias:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Esse comando lista todas as alterações de atribuição de função e de definição de função em uma assinatura para os últimos sete dias e exibe os resultados em uma lista:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Para exibir logs de atividade com a CLI do Azure, use o comando [lista de log de atividades do monitor az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

Esse comando lista os logs de atividade em um grupo de recursos desde a hora de início:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Esse comando lista os logs de atividade para um provedor de recursos de autorização desde a hora de início:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Logs do Azure Monitor

[Os logs de Azure monitor](../log-analytics/log-analytics-overview.md) é outra ferramenta que você pode usar para coletar e analisar alterações de RBAC para todos os seus recursos do Azure. Os logs de Azure Monitor têm os seguintes benefícios:

- Escrever consultas complexas e lógica
- Integrar com alertas, Power BI e outras ferramentas
- Salvar dados por períodos de retenção mais longos
- Referência cruzada com outros logs, como segurança, máquina virtual e personalizados

Aqui estão as etapas básicas para começar:

1. [Criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Configurar a solução Análise do Log de Atividades](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) para seu workspace.

1. [Exibir os logs de atividade](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Uma maneira rápida de navegar até a página de visão geral da solução Análise do Log de Atividades é clicar na opção **log Analytics** .

   ![Opção de logs de Azure Monitor no portal](./media/change-history-report/azure-log-analytics-option.png)

1. Opcionalmente, use a página [pesquisa de Log](../log-analytics/log-analytics-log-search.md) ou o [portal Advanced Analytics](../azure-monitor/log-query/get-started-portal.md) para consultar e exibir os logs. Para obter mais informações sobre essas duas opções, consulte [Página de pesquisa de Log ou o portal Advanced Analytics](../azure-monitor/log-query/portals.md).

Aqui está uma consulta que retorna novas atribuições de função organizadas por provedor de recursos de destino:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Aqui está uma consulta que retorna as alterações de atribuição de função exibidas em um gráfico:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![Logs de atividade usando o portal Advanced Analytics - captura de tela](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Próximos passos
* [Exibir eventos no log de atividades](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorar a atividade da assinatura com o Log de Atividades do Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
