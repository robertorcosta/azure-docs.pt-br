---
title: Gerenciabilidade e monitoramento – atividade de consulta, utilização de recursos
description: Saiba quais recursos estão disponíveis para gerenciar e monitorar a análise de Synapse do Azure. Use o portal do Azure e DMVs (Exibições de Gerenciamento Dinâmico) para entender a atividade de consulta e a utilização de recursos do data warehouse.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d38c0df45da3a751a456846813543a4ce5de98eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416209"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitoramento da utilização de recursos e da atividade de consulta no Azure Synapse Analytics

O Azure Synapse Analytics fornece uma experiência de monitoramento avançada dentro do portal do Azure para insights de superfície sobre sua carga de trabalho de data warehouse. O portal do Azure é a ferramenta recomendada ao monitorar seu data warehouse, pois ele fornece períodos de retenção configuráveis, alertas, recomendações e gráficos e painéis personalizáveis para métricas e logs. O portal também permite que você integre com outros serviços de monitoramento do Azure, como Azure Monitor (logs) com o log Analytics para fornecer uma experiência de monitoramento holística para não apenas sua data warehouse, mas também toda a sua plataforma de análise do Azure para uma experiência de monitoramento integrada. Esta documentação descreve quais recursos de monitoramento estão disponíveis para otimizar e gerenciar sua plataforma de análise com o SQL Analytics.

## <a name="resource-utilization"></a>Utilização de recursos

As métricas a seguir estão disponíveis no portal do Azure para análise de SQL. Essas métricas são exibidas no [Azure Monitor](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics).

| Nome da métrica             | Descrição                                                  | Tipo de agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentual de CPU          | Utilização da CPU em todos os nós para o data warehouse      | Méd., mín., máx.    |
| Porcentagem de E/S de dados      | Utilização de E/S em todos os nós para o data warehouse       | Méd., mín., máx.    |
| Porcentagem de memória       | Utilização de memória (SQL Server) em todos os nós para o data warehouse | Méd., mín., máx.   |
| Consultas ativas          | Número de consultas ativas em execução no sistema             | SUM              |
| Consultas em fila          | Número de consultas em fila aguardando para iniciar a execução          | SUM              |
| Conexões bem sucedidas  | Número de conexões bem-sucedidas (logons) no banco de dados | Soma, contagem       |
| Conexões com falha      | Número de conexões com falha (logons) no banco de dados | Soma, contagem       |
| Bloqueado pelo firewall     | Número de logons para o data warehouse que foram bloqueados     | Soma, contagem       |
| Limite de DWU               | Objetivo de nível de serviço do data warehouse                | Méd., mín., máx.    |
| Porcentagem de DWU          | Máximo entre o percentual de CPU e o percentual de E/S de dados        | Méd., mín., máx.    |
| DWU usado                | Limite de DWU * percentual de DWU                                   | Méd., mín., máx.    |
| Percentual de ocorrência no cache    | (ocorrências no cache/perda no cache) * 100, em que ocorrências no cache é a soma de todas as ocorrências de segmentos columnstore no cache SSD local e a perda no cache são as perdas de segmentos columnstore no cache SSD local somadas entre todos os nós | Méd., mín., máx.    |
| Percentual de cache usado   | (cache usado / capacidade de cache) * 100, em que o cache usado é a soma de todos os bytes no cache SSD local entre todos os nós e a capacidade de cache é a soma da capacidade de armazenamento do cache SSD local entre todos os nós | Méd., mín., máx.    |
| Porcentagem de local de tempdb | Utilização de tempdb local em todos os nós de computação - os valores são emitidos a cada cinco minutos | Méd., mín., máx.    |
| Tamanho do armazenamento de dados (GB) | Tamanho total do banco de dados. Isso inclui o espaço usado, reservado e não alocado. O espaço não alocado é mantido para o banco de dados otimizar o desempenho de consulta e de carga. | SUM |
| Tamanho da recuperação de desastre (GB) | Tamanho total do backup geográfico realizado a cada 24 horas | SUM |
| Tamanho do armazenamento de instantâneos (GB) | Tamanho total dos instantâneos obtidos para fornecer pontos de restauração de banco de dados. Isso inclui instantâneos automatizados e definidos pelo usuário. | SUM |

Itens a serem considerados ao exibir métricas e definir alertas:

- O DWU usado representa apenas uma **representação de alto nível do uso** no pool do SQL e não se destina a ser um indicador abrangente de utilização. Para determinar se é necessário escalar ou reduzir verticalmente, considere todos os fatores que podem ser afetados por DWU, como simultaneidade, memória, tempdb e capacidade de cache adaptável. É recomendável [executar sua carga de trabalho em diferentes configurações de DWU](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) para determinar o que funciona melhor para atender aos seus objetivos de negócios.
- Conexões com falha e bem-sucedidas são relatadas para um determinado data warehouse-não para o servidor lógico
- A porcentagem de memória reflete a utilização mesmo que a data warehouse esteja no estado ocioso-ela não reflete o consumo de memória de carga de trabalho ativa. Use e acompanhe essa métrica junto com outras pessoas (tempdb, cache Gen2) para tomar uma decisão holística se o dimensionamento da capacidade de cache adicional aumentará o desempenho da carga de trabalho para atender às suas necessidades.

## <a name="query-activity"></a>Atividade de consulta

Para uma experiência programática ao monitorar a análise de SQL por meio de T-SQL, o serviço fornece um conjunto de DMVs (exibições de gerenciamento dinâmico). Essas exibições são úteis ao ativamente resolver problemas e identificar gargalos de desempenho com sua carga de trabalho.

Para exibir a lista de DMVs que se aplicam ao Synapse SQL, consulte esta [documentação](../sql/reference-tsql-system-views.md#sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Log de diagnósticos e métricas 

As métricas e os logs podem ser exportados para Azure Monitor, especificamente o componente de [logs de Azure monitor](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e podem ser acessados programaticamente por meio de [consultas de log](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A latência de log para análise de SQL é de cerca de 10-15 minutos. Para obter mais detalhes sobre os fatores que impactam a latência, visite a documentação a seguir.

## <a name="next-steps"></a>Próximas etapas

O guia de instruções a seguir descreve cenários comuns e casos de uso ao monitorar e gerenciar seus data warehouse:

- [Monitorar sua carga de trabalho do data warehouse com DMVs](sql-data-warehouse-manage-monitor.md)
