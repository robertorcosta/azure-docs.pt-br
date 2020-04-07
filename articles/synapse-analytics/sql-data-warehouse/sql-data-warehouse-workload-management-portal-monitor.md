---
title: Monitoramento do portal de gerenciamento de cargas de trabalho
description: Orientação para monitoramento do portal de gerenciamento de carga de trabalho no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a79e6fb2be717b5ecee243b26824039630e1a416
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744278"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics – Monitoramento do Portal de Gerenciamento de Cargas (Preview)

Este artigo explica como monitorar a utilização de recursos do [grupo de carga de trabalho](sql-data-warehouse-workload-isolation.md#workload-groups) e a atividade de consulta.
Para obter detalhes sobre como configurar o Azure Metrics Explorer, consulte o [artigo Do Azure Metrics Explorer.](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  Consulte a seção [de utilização de recursos](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) na documentação do Azure Synapse Analytics Monitoring para obter detalhes sobre como monitorar o consumo de recursos do sistema.
Existem duas categorias diferentes de métricas de grupo de carga de trabalho fornecidas para monitorar o gerenciamento da carga de trabalho: alocação de recursos e atividade de consulta.  Essas métricas podem ser divididas e filtradas por grupo de carga de trabalho.  As métricas podem ser divididas e filtradas com base em se são definidas pelo sistema (grupos de carga de trabalho de classe de recursos) ou definidas pelo usuário (criadas pelo usuário com sintaxe [CREATE WORKLOAD GROUP).](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="workload-management-metric-definitions"></a>Definições métricas de gerenciamento de carga de trabalho

|Nome da métrica                    |Descrição  |Tipo de agregação |
|-------------------------------|-------------|-----------------|
|Porcentagem de recursos de limite efetivo | *O percentual efetivo de recursos de limite* é um limite rígido na porcentagem de recursos acessíveis pelo grupo de carga de trabalho, levando em conta o percentual efetivo de recursos *min* alocado para outros grupos de carga de trabalho. A métrica *de porcentagem de recurso de limite efetivo* é configurada usando o `CAP_PERCENTAGE_RESOURCE` parâmetro na sintaxe CREATE WORKLOAD [GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  O valor efetivo é descrito aqui.<br><br>Por exemplo, se `DataLoads` um grupo `CAP_PERCENTAGE_RESOURCE` de carga de trabalho é criado com = 100 e outro grupo de carga de trabalho é criado com um percentual efetivo de recursos min de 25%, o *percentual de recurso de limite efetivo* para o `DataLoads` grupo de carga de trabalho é de 75%.<br><br>O *percentual de recurso de limite efetivo* determina o limite superior da concorrência (e, portanto, o potencial de throughput) que um grupo de carga de trabalho pode alcançar.  Se for necessário rendimento adicional além do que é relatado atualmente pela métrica *de porcentagem de recursos de limite efetivo,* aumentará o `CAP_PERCENTAGE_RESOURCE`, diminua o `MIN_PERCENTAGE_RESOURCE` de outros grupos de carga de trabalho ou aumente a instância para adicionar mais recursos.  Diminuir `REQUEST_MIN_RESOURCE_GRANT_PERCENT` o pode aumentar a concorrência, mas pode não aumentar a produção geral.| Min |
|Porcentagem de recursos min eficaz |*O percentual efetivo de recursos* mínimos é o percentual mínimo de recursos reservados e isolados para o grupo de carga de trabalho levando em conta o mínimo de nível de serviço.  A métrica de porcentagem de recurso `MIN_PERCENTAGE_RESOURCE` min eficaz é configurada usando o parâmetro na sintaxe [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  O valor efetivo é descrito [aqui.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values)<br><br>Use o tipo de agregação De soma quando essa métrica não for filtrada e não dividida para monitorar o isolamento total da carga de trabalho configurado no sistema.<br><br>O *percentual de recurso min efetivo* determina o limite inferior da concorrência garantida (e, portanto, throughput garantido) que um grupo de carga de trabalho pode alcançar.  Se fornecessário recursos garantidos adicionais além do que é relatado `MIN_PERCENTAGE_RESOURCE` atualmente pela métrica de porcentagem de recursos *min efetivo,* aumente o parâmetro configurado para o grupo de carga de trabalho.  Diminuir `REQUEST_MIN_RESOURCE_GRANT_PERCENT` o pode aumentar a concorrência, mas pode não aumentar a produção geral. |Min|
|Consultas ativas do grupo de trabalho  |Essa métrica relata as consultas ativas dentro do grupo de carga de trabalho.  O uso dessa métrica não filtrada e não dividida exibe todas as consultas ativas em execução no sistema.|SUM         |
|Alocação do grupo de carga de trabalho por percentual máximo de recursos |Essa métrica exibe a alocação percentual de recursos em relação ao *grupo de recursos de limite efetivo* por grupo de carga de trabalho.  Esta métrica fornece a utilização efetiva do grupo de carga de trabalho.<br><br>Considere um `DataLoads` grupo de carga de trabalho com um *percentual de recurso de limite efetivo* de 75% e um `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurado em 25%.  A *alocação do grupo workload por valor máximo de percentual de recurso* filtrado `DataLoads` seria de 33% (25% / 75%) se uma única consulta estivesse sendo executado neste grupo de carga de trabalho.<br><br>Use esta métrica para identificar a utilização de um grupo de carga de trabalho.  Um valor próximo de 100% indica que todos os recursos disponíveis para o grupo de carga de trabalho estão sendo usados.  Além disso, a *métrica de consultas do grupo Workload enfileirada* para o mesmo grupo de carga de trabalho mostrando um valor maior que zero indicaria que o grupo de carga de trabalho utilizaria recursos adicionais se alocado.  Por outro lado, se essa métrica for consistentemente baixa e as consultas ativas do *grupo workload forem* baixas, o grupo de carga de trabalho não está sendo utilizado.  Esta situação é especialmente problemática se *o percentual de recurso de limite efetivo* for maior que zero, pois isso indicaria isolamento da carga de trabalho [subutilizado](#underutilized-workload-isolation).|Min |
|Alocação do grupo de carga de trabalho por porcentagem do sistema | Esta métrica exibe a alocação percentual de recursos em relação a todo o sistema.<br><br>Considere um `DataLoads` grupo `REQUEST_MIN_RESOURCE_GRANT_PERCENT` de carga de trabalho com um configurado em 25%.  *A alocação do grupo de carga de trabalho por percentual* do sistema filtrado `DataLoads` seria de 25% (25% / 100%) se uma única consulta estivesse sendo executado neste grupo de carga de trabalho.|Min |
|Tempos de tempo out sem saída de tempo de consulta de grupo de carga de trabalho |Consultas para o grupo de carga de trabalho que se estiveram fora do tempo.  Os tempos de consulta relatados por essa métrica são apenas uma vez que a consulta tenha começado a ser executada (não inclui tempo de espera devido ao bloqueio ou espera de recursos).<br><br>O tempo de consulta é `QUERY_EXECUTION_TIMEOUT_SEC` configurado usando o parâmetro na sintaxe [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Aumentar o valor poderia reduzir o número de tempos de consulta.<br><br>Considere aumentar `REQUEST_MIN_RESOURCE_GRANT_PERCENT` o parâmetro para o grupo de carga de trabalho para reduzir a quantidade de intervalos e alocar mais recursos por consulta.  Note, `REQUEST_MIN_RESOURCE_GRANT_PERCENT` o aumento reduz a quantidade de simultude para o grupo de carga de trabalho. |SUM |
|Consultas enfileiradas do grupo de trabalho | Consultas para o grupo de carga de trabalho que estão atualmente na fila esperando para iniciar a execução.  As consultas podem estar na fila porque estão esperando por recursos ou bloqueios.<br><br>As consultas podem estar esperando por inúmeras razões.  Se o sistema estiver sobrecarregado e a demanda de simultâneo for maior do que a disponível, as consultas farão fila.<br><br>Considere adicionar mais recursos ao grupo `CAP_PERCENTAGE_RESOURCE` de carga de trabalho aumentando o parâmetro na declaração [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Se `CAP_PERCENTAGE_RESOURCE` for maior que a métrica *de porcentagem de recurso de limite efetivo,* o isolamento de carga de trabalho configurado para outro grupo de carga de trabalho está afetando os recursos alocados para este grupo de carga de trabalho.  Considere `MIN_PERCENTAGE_RESOURCE` a redução de outros grupos de carga de trabalho ou dimensione a instância para adicionar mais recursos. |SUM |

## <a name="monitoring-scenarios-and-actions"></a>Monitoramento de cenários e ações

Abaixo estão uma série de configurações de gráficos para destacar o uso da métrica de gerenciamento de carga de trabalho para solução de problemas, juntamente com as ações associadas para resolver o problema.

### <a name="underutilized-workload-isolation"></a>Isolamento da carga de trabalho subutilizada

Considere a seguinte configuração de grupo de `wgPriority` carga de trabalho e classificador de carga de trabalho onde um grupo de carga de trabalho nomeado é criado e *o Ceo* `membername` é mapeado para ele usando o `wcCEOPriority` classificador de carga de trabalho.  O `wgPriority` grupo de carga de trabalho tem`MIN_PERCENTAGE_RESOURCE` 25% de isolamento da carga de trabalho configurado para ele (= 25).  Cada consulta enviada pelo *CEO* recebe 5% dos`REQUEST_MIN_RESOURCE_GRANT_PERCENT` recursos do sistema (= 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

O gráfico abaixo está configurado da seguinte forma:<br>
Métrica 1: *Porcentagem de recursos min eficaz* `blue line`(agregação Avg),<br>
Métrica 2: *Alocação do grupo de carga de trabalho por percentual* do sistema (agregação avg), `purple line`<br>
Filtro: [Grupo de carga de trabalho] =`wgPriority`<br>
![subutilizado-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) O gráfico mostra que, com 25% de isolamento da carga de trabalho, apenas 10% está sendo usado em média.  Neste caso, `MIN_PERCENTAGE_RESOURCE` o valor do parâmetro poderia ser reduzido para entre 10 ou 15 e permitir que outras cargas de trabalho no sistema consumissem os recursos.

### <a name="workload-group-bottleneck"></a>Gargalo do grupo de carga de trabalho

Considere a seguinte configuração de grupo de `wgDataAnalyst` carga de trabalho e classificador de carga `wcDataAnalyst` de trabalho onde um grupo de carga de trabalho nomeado é criado e o *DataAnalyst* `membername` é mapeado para ele usando o classificador de carga de trabalho.  O `wgDataAnalyst` grupo de carga de trabalho tem`MIN_PERCENTAGE_RESOURCE` 6% de isolamento da carga`CAP_PERCENTAGE_RESOURCE` de trabalho configurado para ele (= 6) e um limite de recursos de 9% (= 9).  Cada consulta enviada pelo *DataAnalyst* recebe 3% dos`REQUEST_MIN_RESOURCE_GRANT_PERCENT` recursos do sistema (= 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

O gráfico abaixo está configurado da seguinte forma:<br>
Métrica 1: *Porcentagem de recursos de limite* `blue line`efetivo (agregação avg),<br>
Métrica 2: *Alocação do grupo de carga de trabalho por percentual máximo de recursos* (agregação `purple line`avg)<br>
Métrica 3: *Agrupamento de carga de trabalho consultas enfileiradas* (agregação de soma) `turquoise line`<br>
Filtro: [Grupo de carga de trabalho] =`wgDataAnalyst`<br>
![bottle-necked-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) O gráfico mostra que, com um limite de 9% nos recursos, o grupo de carga de trabalho é 90%+ utilizado (da *alocação*do grupo Workload por métrica de porcentagem de recursos máximo ).  Há uma fila constante de consultas como mostrado a partir da métrica de *consultas enfileiradas*do grupo Workload .  Neste caso, aumentar `CAP_PERCENTAGE_RESOURCE` o valor para um valor superior a 9% permitirá que mais consultas sejam executadas simultaneamente.  Aumentando `CAP_PERCENTAGE_RESOURCE` as pressuésidades de que há recursos suficientes disponíveis e não isolados por outros grupos de carga de trabalho.  Verifique se a tampa foi aumentada verificando a *métrica de porcentagem de recurso de limite efetivo*.  Se mais throughput for desejado, `REQUEST_MIN_RESOURCE_GRANT_PERCENT` considere também aumentar o valor para um valor maior que 3.  Aumentar `REQUEST_MIN_RESOURCE_GRANT_PERCENT` o pode permitir que as consultas corfem mais rápido.

## <a name="next-steps"></a>Próximas etapas

- [Partida rápida: Configure o isolamento da carga de trabalho usando O T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [Monitoramento da utilização de recursos](sql-data-warehouse-concept-resource-utilization-query-activity.md)
