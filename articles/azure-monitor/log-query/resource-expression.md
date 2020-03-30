---
title: expressão de recursos() na consulta de log do Azure Monitor | Microsoft Docs
description: A expressão de recursos é usada em uma consulta de log do Azure Monitor centrada em recursos para recuperar dados de vários recursos.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665692"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>expressão de recursos() na consulta de log do Azure Monitor

A `resource` expressão é usada em uma consulta do Azure Monitor [escopo de um recurso](scope.md#query-scope) para recuperar dados de outros recursos. 


## <a name="syntax"></a>Sintaxe

`resource(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: ID de recurso de um recurso.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Recurso | Inclui dados para o recurso. | recursos("/assinaturas/xxxxxxx-xxxx-xxxx-xxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Grupo de recursos ou assinatura | Inclui dados para o recurso e todos os recursos que ele contém.  | recursos("/assinaturas/xxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Observações

* Você deve ter lido acesso ao recurso.


## <a name="examples"></a>Exemplos

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Próximas etapas

- Consulte [o escopo de consulta de log e o intervalo de tempo no Azure Monitor Log Analytics](scope.md) para obter detalhes sobre um escopo de consulta.
- Acesse a documentação completa da [linguagem de consulta Kusto](/azure/kusto/query/).
