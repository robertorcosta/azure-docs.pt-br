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
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072630"
---
### <a name="general-query-limits"></a>Limites de consulta geral

| Limite | Descrição |
|:---|:---|
| Idioma de consulta | O Azure Monitor usa a mesma [linguagem de consulta do Kusto](/azure/kusto/query/) que o Azure Data Explorer. Consulte As diferenças de linguagem de consulta de log do [Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) para elementos de linguagem KQL não suportados no Monitor Do Azure. |
| Regiões do Azure | As consultas de log podem sofrer sobrecarga excessiva quando os dados abrangem espaços de trabalho do Log Analytics em várias regiões do Azure. Consulte [os limites de consulta](../articles/azure-monitor/log-query/scope.md#query-limits) para obter detalhes. |
| Consultas cruzadas de recursos | Número máximo de recursos do Application Insights e espaços de trabalho do Log Analytics em uma única consulta limitada a 100.<br>A consulta de recursos cruzados não é suportada no View Designer.<br>A consulta de recursos cruzados nos alertas de log é compatível com a nova API scheduledQueryRules.<br>Consulte [os limites de consulta de recursos cruzados](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) para obter detalhes. |

### <a name="user-query-throttling"></a>Estrangulamento da consulta do usuário
O Azure Monitor tem vários limites de estrangulamento para proteger contra usuários que enviam um número excessivo de consultas. Tal comportamento pode potencialmente sobrecarregar os recursos de backend do sistema e comprometer a capacidade de resposta do serviço. Os seguintes limites são projetados para proteger os clientes contra interrupções e garantir um nível de serviço consistente. O estrangulamento e os limites do usuário são projetados para impactar apenas o cenário de uso extremo e não devem ser relevantes para o uso típico.


| Medida | Limite por usuário | Descrição |
|:---|:---|:---|
| Consultas simultâneas | 5 | Se já houver 5 consultas em execução para o usuário, quaisquer novas consultas serão colocadas em uma fila de siporias por usuário. Quando uma das consultas em execução terminar, a próxima consulta será retirada da fila e iniciada. Isso não inclui consultas de regras de alerta.
| Tempo na fila de simultuantes | 2,5 minutos | Se uma consulta ficar na fila por mais de 2,5 minutos sem ser iniciada, ela será encerrada com uma resposta de erro HTTP com o código 429. |
| Consultas totais na fila de simultuantes | 40 | Uma vez que o número de consultas na fila chegue a 40, quaisquer consultas adicionais serão rejeitadas com um código de erro HTTP 429. Este número é além das 5 consultas que podem ser executados simultaneamente. |
| Taxa de consulta | 200 consultas por 30 segundos | Esta é a taxa global que as consultas podem ser enviadas por um único usuário para todos os espaços de trabalho.  Esse limite se aplica a consultas programáticas ou consultas iniciadas por partes de visualização, como painéis do Azure e a página de resumo do espaço de trabalho log analytics. |

- Otimize suas consultas conforme descrito no [Optimize consultas de log no Azure Monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Os painéis e as pastas de trabalho podem conter várias consultas em uma única exibição que geram uma explosão de consultas toda vez que carregam ou atualizam. Considere separando-os em vários pontos de vista que carregam sob demanda. 
- Em Power BI, considere extrair apenas resultados agregados em vez de logs brutos.