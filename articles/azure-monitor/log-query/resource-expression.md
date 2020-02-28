---
title: expressão de recurso () na consulta de log de Azure Monitor | Microsoft Docs
description: A expressão de recurso é usada em uma consulta de log de Azure Monitor centrada em recursos para recuperar dados de vários recursos.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665692"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>expressão de recurso () na consulta de log de Azure Monitor

A expressão de `resource` é usada em uma consulta Azure Monitor com [escopo para um recurso](scope.md#query-scope) para recuperar dados de outros recursos. 


## <a name="syntax"></a>Sintaxe

`resource(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: ID de recurso de um recurso.

| Identifier | Descrição | {1&gt;Exemplo&lt;1}
|:---|:---|:---|
| Recurso | Inclui dados para o recurso. | recurso ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/VirtualMachines/MyVM") |
| Grupo de recursos ou assinatura | Inclui dados para o recurso e todos os recursos que ele contém.  | recurso ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>{1&gt;Observações&lt;1}

* Você deve ter acesso de leitura ao recurso.


## <a name="examples"></a>Exemplos

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- Consulte [escopo de consulta de log e intervalo de tempo em Azure Monitor log Analytics](scope.md) para obter detalhes sobre um escopo de consulta.
- Acesse a documentação completa da [linguagem de consulta Kusto](/azure/kusto/query/).
