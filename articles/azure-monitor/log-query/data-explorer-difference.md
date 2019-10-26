---
title: Diferenças na linguagem de consulta de log do Azure Monitor | Microsoft Docs
description: Informações de referência para a linguagem de consulta Kusto usada pelo Azure Monitor. Inclui elementos adicionais específicos ao Azure Monitor e elementos sem suporte às consultas do Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 16a221c0530d65e11589ac272a8e032de0cd3d2b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933095"
---
# <a name="azure-monitor-log-query-language-differences"></a>Diferenças na linguagem de consulta de log do Azure Monitor

Enquanto os [logs no Azure Monitor](log-query-overview.md) são criados no [Azure Data Explorer](/azure/data-explorer) e usam a [mesma linguagem de consulta Kusto](/azure/kusto/query), a versão da linguagem tem algumas diferenças. Este artigo identifica os elementos que são diferentes entre a versão do idioma usado para o Data Explorer e a versão usada para consultas de log do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementos KQL não suportados no Azure Monitor
As seções a seguir descrevem os elementos da linguagem de consulta Kusto não compatíveis com o Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instruções não compatíveis com o Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parâmetros de consulta](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funções não compatíveis com o Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores não compatíveis com o Azure Monitor

* [Junção entre clusters](/azure/kusto/query/joincrosscluster)
* [Operador externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plug-ins não compatíveis com o Azure Monitor

* [Plug-in do Python](/azure/kusto/query/pythonplugin)
* [Plug-in do sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Operadores adicionais no Azure Monitor
Os seguintes operadores dão suporte a recursos específicos do Azure Monitor e não estão disponíveis fora do Azure Monitor.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Próximos passos

- Obtenha referências para diferentes [recursos para escrever consultas de log do Azure Monitor](query-language.md).
- Acesse a [documentação de referência completa da linguagem de consulta Kusto](/azure/kusto/query/).
