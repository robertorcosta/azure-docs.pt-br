---
title: Expressão workspace() nas consultas de log do Azure Monitor | Microsoft Docs
description: A expressão workspace é usada em uma consulta de log do Azure Monitor para recuperar dados de um workspace específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: f26b9534fbf95cc301ae782d47ab7030988fa469
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932846"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>Expressão workspace() nas consultas de log do Azure Monitor

A expressão `workspace` é usada em uma consulta do Azure Monitor para recuperar dados de um workspace específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura. Isso é útil para incluir dados de log em uma consulta do Application Insights e para consultar dados em vários workspaces em uma consulta de log.


## <a name="syntax"></a>Sintaxe

`workspace(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: identifica o workspace usando um dos formatos na tabela a seguir.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível do workspace (também conhecido como "nome do componente") | workspace("contosoretail") |
| Nome Qualificado | Nome completo do workspace no formato: "subscriptionName/resourceGroup/componentName" | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | GUID do workspace | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID do recurso do Azure | Identificador do recurso do Azure | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Notas

* Você deve ter acesso de leitura ao workspace.
* Uma expressão relacionada é `app`, que permite consultar entre aplicativos do Application Insights.

## <a name="examples"></a>Exemplos

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Próximos passos

- Consulte a [expressão do aplicativo](app-expression.md) para se referir a um aplicativo Application insights.
- Leia mais sobre como os [dados do Azure Monitor](log-query-overview.md) são armazenados.
- Acesse a documentação completa da [linguagem de consulta Kusto](/azure/kusto/query/).
