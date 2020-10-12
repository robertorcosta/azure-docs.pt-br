---
title: Logs no Azure Monitor | Microsoft Docs
description: Descreve os logs no Azure Monitor, que são usados para análise avançada de dados de monitoramento.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032751"
---
# <a name="azure-monitor-logs-overview"></a>Visão geral dos logs de Azure Monitor
Os logs de Azure Monitor são um recurso de Azure Monitor que coleta e organiza dados de log de uma variedade de fontes e as disponibiliza para análise usando uma linguagem de consulta sofisticada. Os dados de fontes diferentes podem ser consolidados em um único espaço de trabalho e analisados juntos para executar tarefas e análise de tendências, alertas e visualização.

## <a name="relationship-to-azure-monitor-metrics"></a>Relação para Azure Monitor métricas
Azure Monitor métricas armazena dados numéricos em um banco de dado de série temporal, o que torna esses dados mais leves do que Azure Monitor logs e capazes de dar suporte a cenários quase em tempo real, tornando-os particularmente úteis para alertas e detecção rápida de problemas. No entanto, as métricas só podem armazenar dados numéricos em uma determinada estrutura, enquanto os logs podem armazenar uma variedade de tipos de dados diferentes, cada um com sua própria estrutura. Você também pode executar análise complexa nos dados de logs usando consultas de log que não podem ser usadas para análise de dados de métricas.

Os dados numéricos geralmente são enviados de fontes de dados para logs além das métricas. Embora haja um custo adicional para coletar e reter esses dados nos logs, ele permite que você inclua dados de métrica em consultas de log e analise-os com os outros dados de monitoramento.

## <a name="relationship-to-azure-data-explorer"></a>Relação com o Azure Data Explorer
Os logs de Azure Monitor baseiam-se na Data Explorer do Azure. Um espaço de trabalho Log Analytics é aproximadamente o equivalente de um banco de dados no Azure Data Explorer, as tabelas são estruturadas da mesma maneira e ambas usam a mesma linguagem de consulta Kusto (KQL). A experiência de usar o Log Analytics para trabalhar com Azure Monitor consultas no portal do Azure é semelhante à experiência usando a interface do usuário da Web do Azure Data Explorer. Você pode até mesmo [incluir dados de um espaço de trabalho log Analytics em uma consulta de data Explorer do Azure](/azure/data-explorer/query-monitor-data). 


## <a name="structure-of-data"></a>Estrutura de dados
Os dados coletados por logs de Azure Monitor são armazenados em um [espaço de trabalho log Analytics](./design-logs-deployment.md) que contém várias tabelas que armazenam dados de uma fonte específica. O espaço de trabalho define a localização geográfica dos dados, direitos de acesso que definem quais usuários podem acessar dados e definições de configuração, como o tipo de preço e a retenção de dados. Você pode usar um único espaço de trabalho para todos os seus dados de monitoramento ou criar vários espaços de trabalho, dependendo de seus requisitos. Consulte [projetando sua implantação do Azure monitor logs](design-logs-deployment.md) em considerações para criar vários espaços de trabalho.

Cada espaço de trabalho contém várias tabelas que são organizadas em colunas separadas com várias linhas de dados. Cada tabela é definida por um conjunto exclusivo de colunas que são compartilhadas pelas linhas de dados fornecidas pela fonte de dados. 

[![Estrutura de logs de Azure Monitor](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Os dados de log de Application Insights também são armazenados em logs de Azure Monitor, mas são armazenados diferentes dependendo de como seu aplicativo é configurado. Para um aplicativo baseado em espaço de trabalho, os dados são armazenados em um espaço de trabalho Log Analytics em um conjunto padrão de tabelas para manter dados, como solicitações de aplicativo, exceções e exibições de página. Vários aplicativos podem usar o mesmo espaço de trabalho. Para um aplicativo clássico, os dados não são armazenados em um espaço de trabalho Log Analytics. Ele usa a mesma linguagem de consulta e você cria e executa consultas usando a mesma ferramenta de Log Analytics no portal do Azure. No entanto, os dados para aplicativos clássicos são armazenados separadamente uns dos outros. Sua estrutura geral é igual a aplicativos baseados em espaço de trabalho, embora os nomes de tabela e coluna sejam diferentes. Consulte [alterações de recursos com base no espaço de trabalho](../app/apm-tables.md) para obter uma comparação detalhada dos dois.


> [!NOTE]
> Ainda fornecemos compatibilidade total com versões anteriores para as consultas de recursos clássicos, as pastas de trabalho e os alertas baseados em log do Application Insights na experiência do Application Insights. Para consultar/exibir em relação à [nova estrutura/esquema de tabela baseada em espaço de trabalho](../app/apm-tables.md), você deverá primeiro navegar para seu espaço de trabalho do Log Analytics. Durante a visualização, selecione **Logs** de dentro dos painéis do Application Insights para ter acesso à experiência clássica de consulta do Application Insights. Consulte [escopo da consulta](../log-query/scope.md) para obter mais detalhes.


[![Estrutura de logs de Azure Monitor para Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>Consultas de logs
Os dados são recuperados de um espaço de trabalho Log Analytics usando uma [consulta de log](../log-query/log-query-overview.md) que é uma solicitação somente leitura para processar dados e retornar resultados. As consultas de log são escritas em [KQL (linguagem de consulta Kusto)](/azure/data-explorer/kusto/query/), que é a linguagem de consulta usada pelo data Explorer do Azure. Use Log Analytics, que é uma ferramenta na portal do Azure para editar e executar consultas de log e analisar interativamente seus resultados. Você pode usar as consultas que você cria para dar suporte a outros recursos no Azure Monitor, como alertas de consulta de log e pastas de trabalho.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Fontes de dados para logs de Azure Monitor
O Azure Monitor coleta dados de log de uma variedade de fontes, incluindo recursos em Azure Monitor e agentes em execução em máquinas virtuais. Veja [o que é monitorado pelo Azure monitor?](../monitor-reference.md) para obter uma lista completa de fontes de dados que enviam dados para um espaço de trabalho do log Analytics.



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log Analytics.
- Saiba mais sobre as [métricas no Azure Monitor](data-platform-metrics.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.

