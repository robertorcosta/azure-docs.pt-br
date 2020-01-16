---
title: Gerenciabilidade e monitoramento – atividade de consulta, utilização de recursos
description: Saiba quais recursos estão disponíveis para gerenciar e monitorar o SQL Data Warehouse do Azure. Use o portal do Azure e DMVs (Exibições de Gerenciamento Dinâmico) para entender a atividade de consulta e a utilização de recursos do data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 01/14/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 366d170a4caf9ee7428b68d71f910c65356038ff
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024529"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorando a atividade de consulta e a utilização de recursos no SQL Data Warehouse do Azure
O SQL Data Warehouse do Azure oferece uma rica experiência de monitoramento no portal do Azure para gerar insights para sua carga de trabalho do data warehouse. O portal do Azure é a ferramenta recomendada ao monitorar seu data warehouse, pois ele fornece períodos de retenção configuráveis, alertas, recomendações e gráficos e painéis personalizáveis para métricas e logs. O portal também permite que você integre com outros serviços de monitoramento do Azure, como o OMS (Operations Management Suite) e Azure Monitor (logs) para fornecer uma experiência de monitoramento holística para não apenas seus data warehouse, mas também toda a análise do Azure plataforma para uma experiência de monitoramento integrada. Esta documentação descreve quais recursos de monitoramento estão disponíveis para otimizar e gerenciar sua plataforma de análise com o SQL Data Warehouse. 

## <a name="resource-utilization"></a>Utilização de recursos 
As seguintes métricas estão disponíveis no portal do Azure para SQL Data Warehouse. Essas métricas são exibidas no [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Nome da métrica             | Description                                                  | Tipo de agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentual de CPU          | Utilização da CPU em todos os nós para o data warehouse      | Méd., mín., máx.    |
| Porcentagem de E/S de dados      | Utilização de E/S em todos os nós para o data warehouse       | Méd., mín., máx.    |
| Porcentagem de memória       | Utilização de memória (SQL Server) em todos os nós para o data warehouse | Méd., mín., máx.   |
| Consultas ativas          | Número de consultas ativas em execução no sistema             | SUM              |
| Consultas em fila          | Número de consultas em fila aguardando para iniciar a execução          | SUM              |
| Conexões bem sucedidas  | Número de conexões bem-sucedidas com os dados                 | Soma, contagem       |
| Conexões com falha      | Número de conexões com falha com o data warehouse           | Soma, contagem       |
| Bloqueado pelo firewall     | Número de logons para o data warehouse que foram bloqueados     | Soma, contagem       |
| Limite de DWU               | Objetivo de nível de serviço do data warehouse                | Méd., mín., máx.    |
| Porcentagem de DWU          | Máximo entre o percentual de CPU e o percentual de E/S de dados        | Méd., mín., máx.    |
| DWU usado                | Limite de DWU * percentual de DWU                                   | Méd., mín., máx.    |
| Percentual de ocorrência no cache    | (ocorrências no cache/perda no cache) * 100, em que ocorrências no cache é a soma de todas as ocorrências de segmentos columnstore no cache SSD local e a perda no cache são as perdas de segmentos columnstore no cache SSD local somadas entre todos os nós | Méd., mín., máx.    |
| Percentual de cache usado   | (cache usado / capacidade de cache) * 100, em que o cache usado é a soma de todos os bytes no cache SSD local entre todos os nós e a capacidade de cache é a soma da capacidade de armazenamento do cache SSD local entre todos os nós | Méd., mín., máx.    |
| Porcentagem de local de tempdb | Utilização de tempdb local em todos os nós de computação - os valores são emitidos a cada cinco minutos | Méd., mín., máx.    |

Itens a serem considerados ao exibir métricas e definir alertas:

- Conexões com falha e bem-sucedidas são relatadas para um determinado data warehouse-não para o servidor lógico
- A porcentagem de memória reflete a utilização mesmo que a data warehouse esteja no estado ocioso-ela não reflete o consumo de memória de carga de trabalho ativa. Use e acompanhe essa métrica junto com outras pessoas (tempdb, cache Gen2) para tomar uma decisão holística se o dimensionamento da capacidade de cache adicional aumentará o desempenho da carga de trabalho para atender às suas necessidades.


## <a name="query-activity"></a>Atividade de consulta
Para uma experiência de programação ao monitorar o SQL Data Warehouse por meio do T-SQL, o serviço fornece um conjunto de DMVs (Exibições de Gerenciamento Dinâmico). Essas exibições são úteis ao ativamente resolver problemas e identificar gargalos de desempenho com sua carga de trabalho.

Para exibir a lista de DMVs que o SQL Data Warehouse fornece, consulte esta [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Log de diagnósticos e métricas
As métricas e os logs podem ser exportados para Azure Monitor, especificamente o componente de [logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) e podem ser acessados programaticamente por meio de [consultas de log](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). A latência de log para SQL Data Warehouse é de cerca de 10-15 minutos. Para obter mais detalhes sobre os fatores que impactam a latência, visite a documentação a seguir.


## <a name="next-steps"></a>Próximos passos
Os guias de instruções a seguir descrevem cenários e casos de uso comuns ao monitorar e gerenciar seu data warehouse:

- [Monitorar sua carga de trabalho do data warehouse com DMVs](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
