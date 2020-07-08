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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82072630"
---
### <a name="general-query-limits"></a>Limites de consulta geral

| Limite | Descrição |
|:---|:---|
| Idioma de consulta | Azure Monitor usa a mesma [linguagem de consulta Kusto](/azure/kusto/query/) que o data Explorer do Azure. Veja [Azure monitor diferenças de linguagem de consulta de log](../articles/azure-monitor/log-query/data-explorer-difference.md) para elementos de linguagem KQL sem suporte no Azure monitor. |
| Regiões do Azure | As consultas de log podem enfrentar sobrecarga excessiva quando os dados abrangem espaços de trabalho Log Analytics em várias regiões do Azure. Consulte [os limites de consulta](../articles/azure-monitor/log-query/scope.md#query-limits) para obter detalhes. |
| Consultas entre recursos | Número máximo de recursos de Application Insights e espaços de trabalho de Log Analytics em uma única consulta limitada a 100.<br>Não há suporte para a consulta entre recursos no designer de exibição.<br>A consulta de recursos cruzados nos alertas de log é compatível com a nova API scheduledQueryRules.<br>Consulte [limites de consulta entre recursos](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) para obter detalhes. |

### <a name="user-query-throttling"></a>Limitação de consulta de usuário
Azure Monitor tem vários limites de limitação para proteger contra usuários que enviam um número excessivo de consultas. Esse comportamento pode potencialmente sobrecarregar os recursos de back-end do sistema e comprometer a capacidade de resposta do serviço. Os limites a seguir foram projetados para proteger os clientes contra interrupções e garantir um nível de serviço consistente. A limitação e os limites do usuário são projetados para impactar apenas o cenário de uso extremo e não deve ser relevante para o uso típico.


| Medida | Limite por usuário | Descrição |
|:---|:---|:---|
| Consultas simultâneas | 5 | Se já houver cinco consultas em execução para o usuário, todas as novas consultas serão colocadas em uma fila de simultaneidade por usuário. Quando uma das consultas em execução terminar, a próxima consulta será extraída da fila e iniciada. Isso não inclui consultas de regras de alerta.
| Tempo na fila de simultaneidade | 2,5 minutos | Se uma consulta ficar na fila por mais de 2,5 minutos sem ser iniciada, ela será encerrada com uma resposta de erro HTTP com o código 429. |
| Total de consultas na fila de simultaneidade | 40 | Depois que o número de consultas na fila atingir 40, todas as consultas adicionais serão rejeitadas com um código de erro HTTP 429. Esse número é além das cinco consultas que podem ser executadas simultaneamente. |
| Taxa de consulta | 200 consultas por 30 segundos | Essa é a taxa geral que as consultas podem ser enviadas por um único usuário para todos os espaços de trabalho.  Esse limite se aplica a consultas programáticas ou consultas iniciadas por partes de visualização, como painéis do Azure e a página de resumo do Log Analytics espaço de trabalho. |

- Otimize suas consultas conforme descrito em [otimizar consultas de log no Azure monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Os painéis e as pastas de trabalho podem conter várias consultas em uma única exibição que gera uma intermitência de consultas sempre que elas são carregadas ou atualizadas. Considere dividi-los em várias exibições que são carregadas sob demanda. 
- Em Power BI, considere extrair apenas resultados agregados em vez de logs brutos.