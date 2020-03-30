---
title: Tipos de problemas de desempenho de consulta no Banco de Dados SQL do Azure
description: Neste artigo, conheça os tipos de problemas de desempenho de consulta no Banco de Dados SQL do Azure e também aprenda como identificar e resolver dúvidas com esses problemas
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256127"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Tipos detectáveis de gargalos de desempenho de consulta no Banco de Dados SQL do Azure

Ao tentar resolver um gargalo de desempenho, comece determinando se o gargalo está ocorrendo enquanto a consulta está em um estado de execução ou em um estado de espera. Diferentes resoluções se aplicam dependendo dessa determinação. Use o diagrama a seguir para ajudar a entender os fatores que podem causar um problema relacionado à execução ou a um problema relacionado à espera. Problemas e resoluções relativas a cada tipo de problema são discutidos neste artigo.

Você pode usar o Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) ou [DMVs](sql-database-monitoring-with-dmvs.md) do SQL Server para detectar esses tipos de gargalos de desempenho.

![Estados da carga de trabalho](./media/sql-database-monitor-tune-overview/workload-states.png)

**Problemas relacionados à execução**: Os problemas relacionados à execução estão geralmente relacionados a problemas de compilação, resultando em um plano de consulta subótimo ou problemas de execução relacionados a recursos insuficientes ou superutilizados.
**Problemas relacionados à espera**: Os problemas relacionados à espera estão geralmente relacionados a:

- Bloqueios (bloqueio)
- E/S
- Contenção relacionada ao uso de TempDB
- Esperas de concessão de memória

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problemas de compilação resultando em um plano de consulta subótimo

Um plano subótimo gerado pelo SQL Query Optimizer pode ser a causa do desempenho lento da consulta. O Otimizador de Consulta SQL pode produzir um plano subótimo devido a um índice ausente, estatísticas obsoletas, uma estimativa incorreta do número de linhas a serem processadas ou uma estimativa imprecisa da memória necessária. Se você souber que a consulta foi executada mais rapidamente no passado ou em outra instância (seja uma instância gerenciada ou uma instância do SQL Server), compare os planos reais de execução para ver se eles são diferentes.

- Identifique quaisquer índices ausentes usando um desses métodos:

  - Use [Insights Inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Consultor de banco de dados](sql-database-advisor.md) para bancos de dados únicos e agrupados.
  - Dmvs. Este exemplo mostra o impacto de um índice ausente, como detectar um [índice ausente](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes) usando DMVs e o impacto da implementação da recomendação de índice ausente.
- Tente aplicar [dicas de consulta,](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) [atualizar estatísticas](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)ou [reconstruir índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) para obter o melhor plano. Habilite a [correção automática do plano](sql-database-automatic-tuning.md) no Banco de Dados SQL do Azure para mitigar automaticamente esses problemas.

  Este [exemplo](sql-database-performance-guidance.md#query-tuning-and-hinting) mostra o impacto de um plano de consulta subótimo devido a uma consulta parametrizada, como detectar essa condição e como usar uma dica de consulta para resolver.

- Tente alterar o nível de compatibilidade do banco de dados e implementar o processamento inteligente de consultas. O SQL Query Optimizer pode gerar um plano de consulta diferente, dependendo do nível de compatibilidade do seu banco de dados. Níveis mais altos de compatibilidade fornecem recursos de processamento de consulta mais [inteligentes.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)

  - Para obter mais informações sobre processamento de consultas, consulte [O Guia de Arquitetura de Processamento de Consulta](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide).
  - Para alterar os níveis de compatibilidade do banco de dados e ler mais sobre as diferenças entre os níveis de compatibilidade, consulte [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Para ler mais sobre estimativa de cardinalidade, veja [Estimativa da Cardinalidade](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Resolução de consultas com planos de execução de consultas subótimos

As seções a seguir discutem como resolver consultas com plano de execução de consulta subótimo.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>Consultas que têm problemas de plano sensível ao parâmetro (PSP)

Um problema de plano sensível a parâmetros (PSP) acontece quando o otimizador de consulta gera um plano de execução de consulta que é ideal apenas para um valor específico de parâmetro (ou conjunto de valores) e o plano em cache não é ideal para valores de parâmetros que são usados em consecutivas Execuções. Planos que não são ideais podem, então, causar problemas de desempenho de consulta e degradar o throughput geral da carga de trabalho.

Para obter mais informações sobre o farejamento de parâmetros e o processamento de consultas, consulte o [guia de arquitetura de processamento de consulta](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Várias soluçãos podem mitigar problemas de PSP. Cada solução tem tradeoffs e desvantagens associadas:

- Use a dica de consulta [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) em cada execução da consulta. Essa solução negocia o tempo de compilação e aumenta a CPU para melhorar a qualidade do plano. A `RECOMPILE` opção muitas vezes não é possível para cargas de trabalho que requerem um alto throughput.
- Use a dica de consulta [OPTION (OPTIMIZE FOR...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor real do parâmetro com um valor típico de parâmetro que produz um plano que seja bom o suficiente para a maioria das possibilidades de valor de parâmetro. Essa opção requer uma boa compreensão dos valores de parâmetro ideais e características de plano associadas.
- Use a dica de consulta [OPTION (OPTIMIZE FOR Unknown)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor real do parâmetro e, em vez disso, use a média do vetor de densidade. Você também pode fazer isso capturando os valores dos parâmetros de entrada em variáveis locais e, em seguida, usando as variáveis locais dentro dos predicados em vez de usar os próprios parâmetros. Para esta correção, a densidade média deve ser *boa o suficiente*.
- Desabilite o parâmetro de farejar inteiramente usando a dica de consulta [DISABLE_PARAMETER_SNIFFING.](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)
- Use a dica de consulta [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para evitar recompilações no cache. Esta solução de solução assume que o plano comum bom o suficiente é o do cache já. Você também pode desativar atualizações automáticas de estatísticas para reduzir as chances de que o bom plano será despejado e um novo plano ruim será compilado.
- Force o plano usando explicitamente a dica de consulta [DO PLANO DE USO](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) reescrevendo a consulta e adicionando a dica no texto de consulta. Ou defina um plano específico usando o Query Store ou ativando a [sintonia automática](sql-database-automatic-tuning.md).
- Substitua o procedimento único por um conjunto aninhado de procedimentos que podem, cada um, ser usado com base em lógica condicional e nos valores de parâmetro associados.
- Crie alternativas de execução de cadeia de caracteres dinâmica para uma definição de procedimento estático.

Para obter mais informações sobre como resolver problemas de PSP, consulte estes posts no blog:

- [Sinto cheiro de parâmetro.](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. SQL dinâmico vs. procedimentos vs. qualidade do plano para consultas parametrizadas](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Técnicas de otimização de consulta SQL no SQL Server: Farejador de parâmetros](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Compilar atividade causada por parametrização inadequada

Quando uma consulta tem literais, ou o mecanismo do banco de dados parametriza automaticamente a instrução ou um usuário parametriza explicitamente a instrução para reduzir o número de compilações. Um alto número de compilações para uma consulta usando o mesmo padrão, mas valores literais diferentes podem resultar em alto uso da CPU. Da mesma forma, se você apenas parametrizar parcialmente uma consulta que continua a ter literais, o mecanismo do banco de dados não parametriza ainda mais a consulta.

Aqui está um exemplo de uma consulta parcialmente parametrizada:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Neste exemplo, `t1.c1` `@p1`toma, `t2.c2` mas continua a tomar GUID como literal. Neste caso, se você alterar `c2`o valor para , a consulta é tratada como uma consulta diferente, e uma nova compilação acontecerá. Para reduzir compilações neste exemplo, você também parametrizaria o GUID.

A consulta a seguir mostra a contagem de consultas por hash de consulta para determinar se uma consulta está adequadamente parametrizada:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>Fatores que afetam as mudanças no plano de consulta

Uma recompilação de plano de execução de consulta pode resultar em um plano de consulta gerado que difere do plano em cache original. Um plano original existente pode ser recompilado automaticamente por várias razões:

- As alterações no esquema são referenciadas pela consulta
- As alterações de dados nas tabelas são referenciadas pela consulta
- As opções de contexto de consulta foram alteradas

Um plano compilado pode ser ejetado do cache por várias razões, tais como:

- Instância reinicializa
- Alterações de configuração com escopo de banco de dados
- Demanda de memória
- Pedidos explícitos para limpar o cache

Se você usar uma dica RECOMPILE, um plano não será armazenado em cache.

Uma recompilação (ou compilação recente após o despejo de cache) ainda pode resultar na geração de um plano de execução de consulta que seja idêntico ao original. Quando o plano muda do plano anterior ou original, essas explicações são prováveis:

- **Design físico alterado**: Por exemplo, os índices recém-criados cobrem de forma mais eficaz os requisitos de uma consulta. Os novos índices podem ser usados em uma nova compilação se o otimizador de consulta decidir que o uso desse novo índice é mais ideal do que usar a estrutura de dados que foi originalmente selecionada para a primeira versão da execução da consulta. Qualquer alteração física nos objetos referenciados pode resultar em uma nova escolha de plano no momento da compilação.

- **Diferenças de recursos**do servidor : Quando um plano em um sistema difere do plano em outro sistema, a disponibilidade de recursos, como o número de processadores disponíveis, pode influenciar qual plano é gerado. Por exemplo, se um sistema tiver mais processadores, um plano paralelo pode ser escolhido.

- **Estatísticas diferentes**: As estatísticas associadas aos objetos referenciados podem ter mudado ou podem ser materialmente diferentes das estatísticas do sistema original. Se as estatísticas mudarem e uma recompilação acontecer, o otimizador de consulta usará as estatísticas a partir de quando elas mudaram. As distribuições e frequências de dados das estatísticas revisadas podem diferir das da compilação original. Essas mudanças são usadas para criar estimativas de cardinalidade. (*Estimativas de cardinalidade* são o número de linhas que devem fluir através da árvore de consulta lógica.) Mudanças nas estimativas de cardinalidade podem levá-lo a escolher diferentes operadores físicos e ordens associadas de operações. Mesmo pequenas alterações nas estatísticas podem resultar em um plano de execução de consulta alterado.

- **Nível de compatibilidade de banco de dados alterado ou versão do estimador de cardinalidade**: Alterações no nível de compatibilidade do banco de dados podem permitir novas estratégias e recursos que podem resultar em um plano de execução de consulta diferente. Além do nível de compatibilidade do banco de dados, um sinalizador de rastreamento desativado ou habilitado 4199 ou um estado alterado da configuração com escopo de banco de dados QUERY_OPTIMIZER_HOTFIXES também podem influenciar as opções do plano de execução de consultas no momento da compilação. As bandeiras trace 9481 (força legado CE) e 2312 (força padrão CE) também afetam o plano.

## <a name="resource-limits-issues"></a>Problemas de limites de recursos

O desempenho lento da consulta não relacionado a planos de consulta subótimos e índices ausentes geralmente estão relacionados a recursos insuficientes ou superutilizados. Se o plano de consulta for ideal, a consulta (e o banco de dados) podem estar atingindo os limites de recursos para o banco de dados, pool elástico ou instância gerenciada. Um exemplo pode ser o excesso de throughput de gravação de registro para o nível de serviço.

- Detectando problemas de recursos usando o portal Azure: Para ver se os limites de recursos são o problema, consulte [o monitoramento de recursos do Banco de Dados SQL](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring). Para bancos de dados únicos e pools elásticos, consulte [recomendações de desempenho do Database Advisor](sql-database-advisor.md) e [Insights de desempenho de consulta](sql-database-query-performance.md).
- Detectando limites de recursos usando [insights inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Detectando problemas de recursos usando [DMVs:](sql-database-monitoring-with-dmvs.md)

  - O [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) Detran retorna o consumo de CPU, I/O e memória para um banco de dados SQL. Existe uma linha para cada intervalo de 15 segundos, mesmo que não haja atividade no banco de dados. Dados do histórico são mantidos por uma hora.
  - O [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) Detran retorna os dados de uso e armazenamento da CPU para o Azure SQL Database. Os dados são coletados e agregados em intervalos de cinco minutos.
  - [Muitas consultas individuais que consomem cumulativamente alta utilização da CPU](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

Se você identificar o problema como recurso insuficiente, você pode atualizar recursos para aumentar a capacidade do seu banco de dados SQL para absorver os requisitos da CPU. Para obter mais informações, consulte [Dimensionar recursos de banco de dados único sql no Banco de Dados SQL do Azure](sql-database-single-database-scale.md) e [dimensionar recursos elásticos de pool no Banco de Dados SQL do Azure](sql-database-elastic-pool-scale.md). Para obter informações sobre como dimensionar uma instância gerenciada, consulte [limites de recursos de nível de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemas de desempenho causados pelo aumento do volume de carga de trabalho

Um aumento no tráfego de aplicativos e no volume de carga de trabalho pode causar um aumento no uso da CPU. Mas você deve ter cuidado para diagnosticar corretamente este problema. Quando você vir um problema de ALTA CPU, responda a essas perguntas para determinar se o aumento é causado por alterações no volume de carga de trabalho:

- As consultas do aplicativo são a causa do problema da CPU?
- Para as [principais consultas que consomem CPU, você pode identificar:](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)

  - Vários planos de execução estavam associados à mesma consulta? Se sim, por quê?
  - Para consultas com o mesmo plano de execução, os tempos de execução foram consistentes? A contagem de execuçãos aumentou? Se assim for, o aumento da carga de trabalho provavelmente está causando problemas de desempenho.

Em resumo, se o plano de execução da consulta não foi executado de forma diferente, mas o uso da CPU aumentou junto com a contagem de execução, o problema de desempenho provavelmente está relacionado a um aumento da carga de trabalho.

Nem sempre é fácil identificar uma mudança de volume de carga de trabalho que está conduzindo um problema na CPU. Considere estes fatores:

- **Uso de recursos alterado**: Por exemplo, considere um cenário em que o uso da CPU aumentou para 80% por um longo período de tempo. O uso da CPU por si só não significa que o volume de carga de trabalho foi alterado. Regressões no plano de execução de consultas e mudanças na distribuição de dados também podem contribuir para mais uso de recursos, embora o aplicativo execute a mesma carga de trabalho.

- **O aparecimento de uma nova consulta**: Um aplicativo pode conduzir um novo conjunto de consultas em momentos diferentes.

- **Um aumento ou diminuição no número de solicitações**: Este cenário é a medida mais óbvia de uma carga de trabalho. O número de consultas nem sempre corresponde a uma maior utilização de recursos. No entanto, essa métrica ainda é um sinal significativo, assumindo que outros fatores não são alterados.

Use insights inteligentes para detectar [aumentos de carga de trabalho](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) e planejar [regressões](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Problemas relacionados à espera

Uma vez que você eliminou um plano subótimo e problemas *relacionados à espera* que estão relacionados a problemas de execução, o problema de desempenho é geralmente as consultas provavelmente estão esperando por algum recurso. Problemas relacionados à espera podem ser causados por:

- **Bloqueio**:

  Uma consulta pode conter o bloqueio de objetos no banco de dados, enquanto outras tentam acessar os mesmos objetos. Você pode identificar consultas de bloqueio usando [DMVs](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries) ou [Insights Inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md#locking).
- **Problemas de IO**

  As consultas podem estar esperando que as páginas sejam escritas nos dados ou arquivos de log. Neste caso, verifique `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG`as `PAGEIOLATCH_*` estatísticas de espera no Detran. Consulte o uso de DMVs para [identificar problemas de desempenho](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)de IO .
- **Problemas de TempDB**

  Se a carga de trabalho usar tabelas temporárias ou houver vazamentos de TempDB nos planos, as consultas podem ter um problema com o throughput do TempDB. Consulte o uso de DMVs para [identificar problemas tempDB](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problemas relacionados à memória**

  Se a carga de trabalho não tiver memória suficiente, a expectativa de vida da página pode diminuir, ou as consultas podem ter menos memória do que precisam. Em alguns casos, a inteligência incorporada no Query Optimizer corrigirá problemas relacionados à memória. Consulte o uso de DMVs para [identificar problemas de concessão de memória](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Métodos para mostrar as principais categorias de espera

Esses métodos são comumente usados para mostrar as principais categorias de tipos de espera:

- Use insights inteligentes para identificar consultas com degradação de desempenho devido ao [aumento das esperas](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Use [a Loja de Consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para encontrar estatísticas de espera para cada consulta ao longo do tempo. No Repositório de Consultas, aguarde os tipos de espera combinados em categorias de espera. Você pode encontrar o mapeamento das categorias de espera para esperar tipos em [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Use [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) para retornar informações sobre todas as esperas encontradas por threads executados durante uma operação de consulta. Você pode usar esta visão agregada para diagnosticar problemas de desempenho com o Banco de Dados SQL do Azure e também com consultas e lotes específicos. As consultas podem estar esperando por recursos, esperas na fila ou esperas externas.
- Use [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para retornar informações sobre a fila de tarefas que estão esperando algum recurso.

Em cenários de alta CPU, as estatísticas de query Store e wait podem não refletir o uso da CPU se:

- Consultas de alto consumo de CPU ainda estão sendo executadas.
- As consultas de alto consumo de CPU estavam sendo executados quando um failover aconteceu.

Os DMVs que rastreiam a Query Store e aguardam estatísticas mostram resultados apenas para consultas concluídas e com tempo de saída com sucesso. Eles não mostram dados para a execução de declarações até que as declarações terminem. Use a exibição de gerenciamento dinâmico [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para acompanhar as consultas de execução atualmente e o tempo de trabalho associado.

> [!TIP]
> Ferramentas adicionais:
>
> - [TigerToolbox espera e travas](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [usp_whatsup tigertoolbox](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Próximas etapas

[Monitoramento e ajuste de banco de dados SQL](sql-database-monitor-tune-overview.md)