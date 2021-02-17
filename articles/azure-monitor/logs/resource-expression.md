---
title: expressão de recurso () na consulta de log de Azure Monitor | Microsoft Docs
description: A expressão de recurso é usada em uma consulta de log de Azure Monitor centrada em recursos para recuperar dados de vários recursos.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100606565"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>expressão de recurso () na consulta de log de Azure Monitor

A `resource` expressão é usada em uma consulta Azure monitor [com escopo para um recurso](scope.md#query-scope) para recuperar dados de outros recursos. 


## <a name="syntax"></a>Syntax

`resource(`*ID*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: ID de recurso de um recurso.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Recurso | Inclui dados para o recurso. | recurso ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/VirtualMachines/MyVM") |
| Grupo de recursos ou assinatura | Inclui dados para o recurso e todos os recursos que ele contém.  | recurso ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Observações

* Você deve ter acesso de leitura ao recurso.


## <a name="examples"></a>Exemplos

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Próximas etapas

- Consulte [escopo de consulta de log e intervalo de tempo em Azure Monitor log Analytics](scope.md) para obter detalhes sobre um escopo de consulta.
- Acesse a documentação completa da [linguagem de consulta Kusto](/azure/kusto/query/).
