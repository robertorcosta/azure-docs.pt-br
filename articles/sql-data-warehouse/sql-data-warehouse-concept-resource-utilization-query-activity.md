---
title: Gerenciabilidade e monitoramento – atividade de consulta, utilização de recursos
description: Saiba quais recursos estão disponíveis para gerenciar e monitorar a análise de Synapse do Azure. Use o portal do Azure e DMVs (Exibições de Gerenciamento Dinâmico) para entender a atividade de consulta e a utilização de recursos do data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 82bf6f9a78a46659cc2e0955895c6e1a6e6eb3aa
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096635"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitoramento da utilização de recursos e da atividade de consulta no Azure Synapse Analytics
O Azure Synapse Analytics fornece uma experiência de monitoramento avançada dentro do portal do Azure para insights de superfície sobre sua carga de trabalho de data warehouse. O portal do Azure é a ferramenta recomendada ao monitorar seu data warehouse, pois ele fornece períodos de retenção configuráveis, alertas, recomendações e gráficos e painéis personalizáveis para métricas e logs. O portal também permite que você integre com outros serviços de monitoramento do Azure, como Azure Monitor (logs) com o log Analytics para fornecer uma experiência de monitoramento holística para não apenas seu data warehouse, mas também toda a sua plataforma de análise do Azure para uma experiência de monitoramento. Esta documentação descreve quais recursos de monitoramento estão disponíveis para otimizar e gerenciar sua plataforma de análise com o SQL Analytics. 

## <a name="resource-utilization"></a>Utilização de recursos 
As métricas a seguir estão disponíveis no portal do Azure para análise de SQL. Essas métricas são exibidas no [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Nome da métrica             | Descrição                                                  | Tipo de agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentual de CPU          | Utilização da CPU em todos os nós para o data warehouse      | Méd., mín., máx.    |
| Porcentagem de E/S de dados      | Utilização de E/S em todos os nós para o data warehouse       | Méd., mín., máx.    |
| Porcentagem de memória       | Utilização de memória (SQL Server) em todos os nós para o data warehouse | Méd., mín., máx.   |
| Consultas ativas          | Número de consultas ativas em execução no sistema             | Sum              |
| Consultas em fila          | Número de consultas em fila aguardando para iniciar a execução          | Sum              |
| Conexões bem sucedidas  | Número de conexões bem-sucedidas com os dados                 | Soma, contagem       |
| Conexões com falha      | Número de conexões com falha com o data warehouse           | Soma, contagem       |
| Bloqueado pelo firewall     | Número de logons para o data warehouse que foram bloqueados     | Soma, contagem       |
| Limite de DWU               | Objetivo de nível de serviço do data warehouse                | Méd., mín., máx.    |
| Porcentagem de DWU          | Máximo entre o percentual de CPU e o percentual de E/S de dados        | Méd., mín., máx.    |
| DWU usado                | Limite de DWU * percentual de DWU                                   | Méd., mín., máx.    |
| Percentual de ocorrência no cache    | (ocorrências no cache/perda no cache) * 100, em que ocorrências no cache é a soma de todas as ocorrências de segmentos columnstore no cache SSD local e a perda no cache são as perdas de segmentos columnstore no cache SSD local somadas entre todos os nós | Méd., mín., máx.    |
| Percentual de cache usado   | (cache usado / capacidade de cache) * 100, em que o cache usado é a soma de todos os bytes no cache SSD local entre todos os nós e a capacidade de cache é a soma da capacidade de armazenamento do cache SSD local entre todos os nós | Méd., mín., máx.    |
| Porcentagem de local de tempdb | Utilização de tempdb local em todos os nós de computação - os valores são emitidos a cada cinco minutos | Méd., mín., máx.    |
| Tamanho do armazenamento de dados | Tamanho total dos dados carregados no banco de dado. Isso inclui dados que residem em tabelas CCI e não CCI em que o tamanho das tabelas não CCI é medido pelo tamanho total do arquivo de banco de dados | Sum |
| Tamanho da recuperação de desastre | Tamanho total do backup geográfico realizado a cada 24 horas | Sum |
| Tamanho do armazenamento de instantâneos | Tamanho total dos instantâneos obtidos para fornecer pontos de restauração de banco de dados. Isso inclui instantâneos automatizados e definidos pelo usuário. | Sum |

Itens a serem considerados ao exibir métricas e definir alertas:

- O DWU usado representa apenas uma **representação de alto nível do uso** no pool do SQL e não se destina a ser um indicador abrangente de utilização. Para determinar se é necessário escalar ou reduzir verticalmente, considere todos os fatores que podem ser afetados por DWU, como simultaneidade, memória, tempdb e capacidade de cache adaptável. É recomendável [executar sua carga de trabalho em diferentes configurações de DWU](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units) para determinar o que funciona melhor para atender aos seus objetivos de negócios.
- Conexões com falha e bem-sucedidas são relatadas para um determinado data warehouse-não para o servidor lógico
- A porcentagem de memória reflete a utilização mesmo que a data warehouse esteja no estado ocioso-ela não reflete o consumo de memória de carga de trabalho ativa. Use e acompanhe essa métrica junto com outras pessoas (tempdb, cache Gen2) para tomar uma decisão holística se o dimensionamento da capacidade de cache adicional aumentará o desempenho da carga de trabalho para atender às suas necessidades.


## <a name="query-activity"></a>Atividade de consulta
Para uma experiência programática ao monitorar a análise de SQL por meio de T-SQL, o serviço fornece um conjunto de DMVs (exibições de gerenciamento dinâmico). Essas exibições são úteis ao ativamente resolver problemas e identificar gargalos de desempenho com sua carga de trabalho.

Para exibir a lista de DMVs que o SQL Analytics fornece, consulte esta [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Log de diagnósticos e métricas
As métricas e os logs podem ser exportados para Azure Monitor, especificamente o componente de [logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) e podem ser acessados programaticamente por meio de [consultas de log](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). A latência de log para análise de SQL é de cerca de 10-15 minutos. Para obter mais detalhes sobre os fatores que impactam a latência, visite a documentação a seguir.


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
Os guias de instruções a seguir descrevem cenários e casos de uso comuns ao monitorar e gerenciar seu data warehouse:

- [Monitorar sua carga de trabalho do data warehouse com DMVs](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
