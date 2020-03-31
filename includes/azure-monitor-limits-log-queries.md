---
title: incluir arquivo
description: incluir arquivo
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795908"
---
| Limite | Descrição |
|:---|:---|
| Idioma de consulta | O Azure Monitor usa a mesma [linguagem de consulta do Kusto](/azure/kusto/query/) que o Azure Data Explorer. Consulte As diferenças de linguagem de consulta de log do [Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) para elementos de linguagem KQL não suportados no Monitor Do Azure. |
| Regiões do Azure | As consultas de log podem sofrer sobrecarga excessiva quando os dados abrangem espaços de trabalho do Log Analytics em várias regiões do Azure. Consulte [os limites de consulta](../articles/azure-monitor/log-query/scope.md#query-limits) para obter detalhes. |
| Consultas cruzadas de recursos | Número máximo de recursos do Application Insights e espaços de trabalho do Log Analytics em uma única consulta limitada a 100.<br>A consulta de recursos cruzados não é suportada no View Designer.<br>A consulta de recursos cruzados nos alertas de log é compatível com a nova API scheduledQueryRules.<br>Consulte [os limites de consulta de recursos cruzados](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) para obter detalhes. |
| Query estrangulamento | Um usuário é limitado a 200 consultas por 30 segundos em qualquer número de espaços de trabalho. Esse limite se aplica a consultas programáticas ou a consultas iniciadas por partes de visualização, como painéis do Azure e a página de resumo do espaço de trabalho log analytics. |
