---
title: Gerenciamento e monitoramento - atividade de consulta, utilização de recursos
description: Saiba quais recursos estão disponíveis para gerenciar e monitorar o Azure Synapse Analytics. Use o portal do Azure e DMVs (Exibições de Gerenciamento Dinâmico) para entender a atividade de consulta e a utilização de recursos do data warehouse.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0b7accec9fdce1ad81a08aee17b37d655409948b
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607569"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorando a utilização de recursos e a atividade de consulta no Azure Synapse Analytics

O Azure Synapse Analytics oferece uma rica experiência de monitoramento dentro do portal Azure para fornecer insights sobre a carga de trabalho do seu data warehouse. O portal do Azure é a ferramenta recomendada ao monitorar seu data warehouse, pois ele fornece períodos de retenção configuráveis, alertas, recomendações e gráficos e painéis personalizáveis para métricas e logs. O portal também permite que você se integre a outros serviços de monitoramento do Azure, como o Azure Monitor (logs) com o Log analytics, para fornecer uma experiência de monitoramento holística não apenas para o seu data warehouse, mas também para toda a sua plataforma de análise do Azure para uma experiência de monitoramento integrada. Esta documentação descreve quais recursos de monitoramento estão disponíveis para otimizar e gerenciar sua plataforma de análise. 

## <a name="resource-utilization"></a>Utilização de recursos

As seguintes métricas estão disponíveis no portal Azure para Synapse SQL. Essas métricas são exibidas no [Azure Monitor](../../azure-monitor/platform/data-collection.md#metrics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).


| Nome da métrica             | Descrição                                                  | Tipo de agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentual de CPU          | Utilização da CPU em todos os nós para o data warehouse      | Avg, Min, Max    |
| Porcentagem de E/S de dados      | Utilização de E/S em todos os nós para o data warehouse       | Avg, Min, Max    |
| Porcentagem de memória       | Utilização de memória (SQL Server) em todos os nós para o data warehouse | Avg, Min, Max   |
| Consultas ativas          | Número de consultas ativas em execução no sistema             | SUM              |
| Consultas enfileiradas          | Número de consultas enfileiradas esperando para começar a executar          | SUM              |
| Conexões bem sucedidas  | Número de conexões bem-sucedidas com os dados                 | Soma, Conde       |
| Conexões com falha      | Número de conexões com falha com o data warehouse           | Soma, Conde       |
| Bloqueado pelo firewall     | Número de logons para o data warehouse que foram bloqueados     | Soma, Conde       |
| Limite de DWU               | Objetivo de nível de serviço do data warehouse                | Avg, Min, Max    |
| Porcentagem de DWU          | Máximo entre o percentual de CPU e o percentual de E/S de dados        | Avg, Min, Max    |
| DWU usado                | Limite de DWU * percentual de DWU                                   | Avg, Min, Max    |
| Percentual de ocorrência no cache    | (ocorrências no cache/perda no cache) * 100, em que ocorrências no cache é a soma de todas as ocorrências de segmentos columnstore no cache SSD local e a perda no cache são as perdas de segmentos columnstore no cache SSD local somadas entre todos os nós | Avg, Min, Max    |
| Percentual de cache usado   | (cache usado / capacidade de cache) * 100, em que o cache usado é a soma de todos os bytes no cache SSD local entre todos os nós e a capacidade de cache é a soma da capacidade de armazenamento do cache SSD local entre todos os nós | Avg, Min, Max    |
| Porcentagem de local de tempdb | Utilização de tempdb local em todos os nós de computação - os valores são emitidos a cada cinco minutos | Avg, Min, Max    |
| Tamanho do armazenamento de dados (GB) | Tamanho total dos dados carregados no banco de dados. Isso inclui dados residentes em tabelas CCI e não-CCI onde o tamanho das tabelas não-CCI é medido pelo tamanho total do arquivo de banco de dados | SUM |
| Tamanho da recuperação de desastres (GB) | Tamanho total do geobackup feito a cada 24 horas | SUM |
| Tamanho de armazenamento instantâneo (GB) | Tamanho total dos instantâneos levados para fornecer pontos de restauração do banco de dados. Isso inclui instantâneos automatizados e definidos pelo usuário. | SUM |

Coisas a considerar ao visualizar métricas e definir alertas:

- O DWU usado representa apenas uma **representação de alto nível de uso** em todo o pool SQL e não deve ser um indicador abrangente de utilização. Para determinar se deve escalar para cima ou para baixo, considere todos os fatores que podem ser impactados pelo DWU, como simultâneo, memória, tempdb e capacidade de cache adaptável. Recomendamos [executar sua carga de trabalho em diferentes configurações de DWU](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) para determinar o que funciona melhor para atender aos seus objetivos de negócios.
- Conexões com falha e sucesso são relatadas para um determinado data warehouse - não para o servidor lógico
- A porcentagem de memória reflete a utilização mesmo que o data warehouse esteja em estado ocioso - ele não reflete o consumo ativo de memória de carga de trabalho. Use e rastreie essa métrica juntamente com outros (tempdb, cache gen2) para tomar uma decisão holística sobre se o dimensionamento para capacidade de cache adicional aumentará o desempenho da carga de trabalho para atender às suas necessidades.

## <a name="query-activity"></a>Atividade de consulta

Para uma experiência programática ao monitorar o Synapse SQL via T-SQL, o serviço fornece um conjunto de DMVs (Dynamic Management Views, visualizações dinâmicas). Essas exibições são úteis ao ativamente resolver problemas e identificar gargalos de desempenho com sua carga de trabalho.

Para visualizar a lista de DMVs que se aplicam ao Synapse SQL, consulte esta [documentação](sql-data-warehouse-reference-tsql-system-views.md#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Log de diagnósticos e métricas

Ambas as métricas e logs podem ser exportados para o Azure Monitor, especificamente o componente logs do [Monitor do Azure](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e podem ser acessados de forma programática através de [consultas de log](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A latência de registro para Synapse SQL é de cerca de 10-15 minutos. Para obter mais detalhes sobre os fatores que afetam a latência, visite a seguinte documentação.

## <a name="next-steps"></a>Próximas etapas

Os guias de instruções a seguir descrevem cenários e casos de uso comuns ao monitorar e gerenciar seu data warehouse:

- [Monitorar sua carga de trabalho do data warehouse com DMVs](/sql-data-warehouse/sql-data-warehouse-manage-monitor?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  
