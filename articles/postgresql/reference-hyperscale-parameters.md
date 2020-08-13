---
title: Parâmetros de servidor – hiperescala (Citus) – banco de dados do Azure para PostgreSQL
description: Parâmetros na API do SQL de hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136704"
---
# <a name="server-parameters"></a>Parâmetros do Servidor

Há vários parâmetros de servidor que afetam o comportamento de hiperescala (Citus), ambos os parâmetros do PostgreSQL padrão e parâmetros específicos para o subdimensionamento (Citus). Para saber mais sobre os parâmetros de configuração do PostgreSQL, você pode visitar a seção [configuração de tempo de execução](http://www.postgresql.org/docs/current/static/runtime-config.html) da documentação do PostgreSQL.

O restante desta referência destina-se a discutir parâmetros de configuração específicos de hiperescala (Citus). Esses parâmetros podem ser definidos no portal do Azure em **parâmetros de nó de trabalho** em **configurações** para um grupo de servidores de hiperescala (Citus).

> [!NOTE]
>
> Os grupos de servidores de hiperescala que executam versões mais antigas do mecanismo do Citus podem não oferecer todos os parâmetros listados abaixo.

## <a name="general-configuration"></a>Configuração geral

### <a name="citususe_secondary_nodes-enum"></a>citus. use \_ os \_ nós secundários (enum)

Define a política a ser usada ao escolher nós para consultas SELECT. Se estiver definido como ' Always ', o planejador consultará somente os nós marcados como ' secundários ' noderole em [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

Os valores com suporte para essa enumeração são:

-   **nunca:** (padrão) todas as leituras acontecem em nós primários.
-   **sempre:** As leituras são executadas em nós secundários e, em vez disso, as instruções INSERT/UPDATE são desabilitadas.

### <a name="cituscluster_name-text"></a>citus. \_ nome do cluster (texto)

Informa ao planejador de nós de coordenador qual cluster ele coordena. Depois que \_ o nome do cluster for definido, o planejador consultará os nós de trabalho nesse cluster sozinha.

### <a name="citusenable_version_checks-boolean"></a>citus. habilitar \_ \_ verificações de versão (booliano)

Atualizar a versão do Citus (hiperescala) requer uma reinicialização do servidor (para selecionar a nova biblioteca compartilhada), seguida pelo comando ALTER EXTENSION UPDATE.  A falha na execução de ambas as etapas pode causar erros ou falhas.
O Citus (hiperescala) valida, assim, a versão do código e a correspondência da extensão e os erros se não tiverem \' .

Esse valor padrão é true e é efetivo no coordenador. Em casos raros, processos complexos de atualização podem exigir a definição desse parâmetro como false, desabilitando assim a verificação.

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus. log \_ Distributed \_ deadlock \_ Detection (booliano)

Se é para registrar em log o processamento relacionado à detecção de deadlocks no log do servidor. O padrão é false.

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus. \_ \_ \_ fator de detecção de deadlock distribuído (ponto flutuante)

Define o tempo de espera antes de verificar se há deadlocks distribuídos. Em particular, o tempo de espera será esse valor multiplicado pela \' configuração de [ \_ tempo limite de deadlock](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) de PostgreSQL s. O valor padrão é `2`. Um valor `-1` desabilita A detecção de deadlock distribuído.

### <a name="citusnode_connection_timeout-integer"></a>\_ \_ tempo limite de conexão de citus. Node (inteiro)

O `citus.node_connection_timeout` GUC define a duração máxima (em milissegundos) para aguardar o estabelecimento da conexão. O Citus (hiperescala) gerará um erro se o tempo limite expirar antes que pelo menos uma conexão de trabalho seja estabelecida. Esse GUC afeta as conexões do coordenador para os trabalhadores e os trabalhadores entre si.

-   Padrão: cinco segundos
-   Mínimo: 10 milissegundos
-   Máximo: uma hora

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>Estatísticas de Consulta

### <a name="citusstat_statements_purge_interval-integer"></a>\_ \_ intervalo de limpeza de instruções citus. stat \_ (Integer)

Define a frequência na qual o daemon de manutenção remove registros de [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) que não correspondem no `pg_stat_statements` . Esse valor de configuração define o intervalo de tempo entre as limpezas em segundos, com um valor padrão de 10. Um valor de 0 desabilita as limpezas.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Esse parâmetro é efetivo no coordenador e pode ser alterado em tempo de execução.

## <a name="data-loading"></a>Carregamento de dados

### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. \_ \_ \_ protocolo de confirmação de vários fragmentos (enum)

Define o protocolo de confirmação a ser usado ao executar a cópia em uma tabela distribuída por hash. Em cada posicionamento de fragmento individual, a cópia é executada em um bloco de transação para garantir que nenhum dado seja ingerido se ocorrer um erro durante a cópia. No entanto, há um caso de falha específico em que a cópia é realizada com êxito em todos os posicionamentos, mas uma falha (de hardware) ocorre antes de todas as transações serem confirmadas. Esse parâmetro pode ser usado para evitar a perda de dados nesse caso escolhendo entre os seguintes protocolos de confirmação:

-   **2pc:** (padrão) as transações nas quais a cópia é executada nos posicionamentos de fragmentos são primeiro preparadas usando \' [a confirmação de duas fases](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) do PostgreSQL e, em seguida, confirmadas. As confirmações com falha podem ser recuperadas ou anuladas manualmente usando COMMIT preparado ou reversão preparada, respectivamente.
    Ao usar 2pc, [ \_ \_ as transações](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) preparadas máximas devem ser aumentadas em todos os trabalhos, normalmente para o mesmo valor que as conexões máximas \_ .
-   **1pc:** As transações em que a cópia é executada nos posicionamentos de fragmentos são confirmadas em uma única rodada. Os dados poderão ser perdidos se uma confirmação falhar depois que a cópia for bem-sucedida em todos os posicionamentos (raras).

### <a name="citusshard_replication_factor-integer"></a>\_fator de replicação citus. Fragment \_ (Integer)

Define o fator de replicação para fragmentos que é, o número de nós nos quais os fragmentos serão colocados e o padrão é 1. Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador. O valor ideal para esse parâmetro depende do tamanho do cluster e da taxa de falha do nó.  Por exemplo, talvez você queira aumentar esse fator de replicação se executar clusters grandes e observar falhas de nó com mais frequência.

### <a name="citusshard_count-integer"></a>citus. \_ contagem de fragmentos (Integer)

Define a contagem de fragmentos para tabelas com particionamento de hash e usa como padrão o 32.  Esse valor é usado pelo [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF ao criar tabelas particionadas por hash. Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador.

### <a name="citusshard_max_size-integer"></a>\_tamanho máximo de citus. fragmento \_ (Integer)

Define o tamanho máximo para o qual um fragmento será aumentado antes de ser dividido e o padrão é 1 GB. Quando o tamanho do arquivo de origem \' (que é usado para preparo) para um fragmento excede esse valor de configuração, o banco de dados garante que um novo fragmento seja criado. Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador.

## <a name="planner-configuration"></a>Configuração do Planner

### <a name="cituslimit_clause_row_fetch_count-integer"></a>\_ \_ \_ contagem de busca de linha de cláusula citus. Limit \_ (inteiro)

Define o número de linhas a serem buscadas por tarefa para otimização da cláusula de limite.
Em alguns casos, selecione consultas com cláusulas de limite pode ser necessário buscar todas as linhas de cada tarefa para gerar resultados. Nesses casos, e onde uma aproximação produziria resultados significativos, esse valor de configuração define o número de linhas a serem buscadas de cada fragmento. As aproximações de limite são desabilitadas por padrão e esse parâmetro é definido como-1. Esse valor pode ser definido em tempo de execução e em vigor no coordenador.

### <a name="cituscount_distinct_error_rate-floating-point"></a>\_ \_ taxa de erro distinta de citus. Count \_ (ponto flutuante)

O Citus (hiperscale) pode calcular a contagem (DISTINCT) aproximada usando a extensão PostgreSQL-HLL. Essa entrada de configuração define a taxa de erro desejada ao calcular a contagem (DISTINCT). 0,0, que é o padrão, desabilita as aproximações da contagem (DISTINCT); e o 1,0 não fornece nenhuma garantia sobre a precisão dos resultados. É recomendável definir esse parâmetro como 0, 5 para obter melhores resultados. Esse valor pode ser definido em tempo de execução e em vigor no coordenador.

### <a name="citustask_assignment_policy-enum"></a>citus. \_ \_ política de atribuição de tarefa (enum)

> [!NOTE]
> Esse GUC é aplicável somente quando [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) é maior que um, ou para consultas em relação a [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Define a política a ser usada ao atribuir tarefas a trabalhadores. O coordenador atribui tarefas a trabalhadores com base em locais de fragmento. Esse valor de configuração especifica a política a ser usada ao fazer essas atribuições.
Atualmente, há três políticas de atribuição de tarefas possíveis que podem ser usadas.

-   **ávido:** A política de ávido é o padrão e pretende distribuir uniformemente as tarefas entre os trabalhadores.
-   **Round Robin:** A política Round Robin atribui tarefas a trabalhadores em um modo Round Robin alternando entre réplicas diferentes. Essa política permite uma melhor utilização do cluster quando a contagem de fragmentos de uma tabela é baixa em comparação com o número de trabalhadores.
-   **primeira réplica:** A política de primeira réplica atribui tarefas com base na ordem de inserção de posicionamentos (réplicas) para os fragmentos. Em outras palavras, a consulta de fragmento para um fragmento é atribuída ao trabalho que tem a primeira réplica desse fragmento.
    Esse método permite que você tenha garantias fortes sobre quais fragmentos serão usados em quais nós (ou seja, garantias de residência de memória mais fortes).

Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador.

## <a name="intermediate-data-transfer"></a>Transferência de Dados intermediários

### <a name="citusbinary_worker_copy_format-boolean"></a>\_ \_ formato de cópia do operador citus. Binary \_ (booliano)

Use o formato de cópia binária para transferir dados intermediários entre os trabalhadores.
Durante junções de tabelas grandes, o Citus (hiperescala) pode ter que reparticionar e embaralhar dados dinamicamente entre diferentes trabalhadores. Por padrão, esses dados são transferidos em formato de texto. Habilitar esse parâmetro instrui o banco de dados a usar o formato de serialização binária do PostgreSQL para transferir esses dados. Esse parâmetro é eficaz nos trabalhos e precisa ser alterado no arquivo PostgreSQL. conf. Depois de editar o arquivo de configuração, os usuários podem enviar um sinal SIGHUP ou reiniciar o servidor para que essa alteração entre em vigor.

### <a name="citusbinary_master_copy_format-boolean"></a>\_formato de cópia mestra citus. Binary \_ \_ (booliano)

Use o formato de cópia binária para transferir dados entre o coordenador e os trabalhadores. Ao executar consultas distribuídas, os trabalhadores transferem seus resultados intermediários para o coordenador para agregação final. Por padrão, esses dados são transferidos em formato de texto. Habilitar esse parâmetro instrui o banco de dados a usar o formato de serialização binária do PostgreSQL para transferir esses dados.
Esse parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

### <a name="citusmax_intermediate_result_size-integer"></a>\_tamanho do resultado intermediário citus. Max \_ \_ (Integer)

O tamanho máximo em KB de resultados intermediários para CTEs que não podem ser empurrados para nós de trabalho para execução e para subconsultas complexas. O padrão é 1 GB, e um valor de-1 significa sem limite.
As consultas que excederem o limite serão canceladas e produzirão uma mensagem de erro.

## <a name="ddl"></a>DDL

### <a name="citusenable_ddl_propagation-boolean"></a>citus. habilitar \_ a \_ propagação de DDL (booliano)

Especifica se as alterações DDL devem ser propagadas automaticamente do coordenador para todos os trabalhadores. O valor padrão é true. Como algumas alterações de esquema exigem um bloqueio exclusivo de acesso em tabelas, e como a propagação automática se aplica a todos os trabalhadores sequencialmente, ela pode tornar um cluster de hiperescala (Citus) temporariamente menos responsivo. Você pode optar por desabilitar essa configuração e propagar as alterações manualmente.

## <a name="executor-configuration"></a>Configuração do executor

### <a name="general"></a>Geral

#### <a name="citusall_modifications_commutative"></a>citus. todas as \_ modificações são \_ comutativas

O Citus (hiperscale) impõe as regras de intercambialidade e adquire os bloqueios apropriados para operações de modificação a fim de garantir a exatidão do comportamento. Por exemplo, ele pressupõe que uma instrução INSERT faz o mudo com outra instrução INSERT, mas não com uma instrução UPDATE ou DELETE. Da mesma forma, ele pressupõe que uma instrução UPDATE ou DELETE não faz o desative com outra instrução UPDATE ou DELETE. Essa precaução significa que as atualizações e exclusões exigem hiperescala (Citus) para adquirir bloqueios mais fortes.

Se você tiver instruções UPDATE que sejam comutadas com suas inserções ou outras atualizações, poderá relaxar essas suposições intercambialidade definindo esse parâmetro como true. Quando esse parâmetro é definido como true, todos os comandos são considerados como comutadores e alegam um bloqueio compartilhado, o que pode melhorar a produtividade geral. Esse parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

#### <a name="citusremote_task_check_interval-integer"></a>citus. \_ \_ \_ intervalo de verificação de tarefa remota (Integer)

Define a frequência na qual o Citus (hiperescala) verifica os status dos trabalhos gerenciados pelo executor do rastreador de tarefas. O padrão é 10 ms. O coordenador atribui tarefas a trabalhadores e, em seguida, verifica regularmente com elas sobre o progresso de cada tarefa \' . Esse valor de configuração define o intervalo de tempo entre duas verificações de resultante. Esse parâmetro é efetivo no coordenador e pode ser definido em tempo de execução.

#### <a name="citustask_executor_type-enum"></a>\_tipo de executor de citus. Task \_ (enum)

O Citus (hiperscale) tem três tipos de executor para executar consultas de seleção distribuídas.  O executor desejado pode ser selecionado definindo esse parâmetro de configuração. Os valores aceitos para esse parâmetro são:

-   **adaptável:** O padrão. É ideal para respostas rápidas a consultas que envolvem agregações e junções colocalizadas que abrangem vários fragmentos.
-   **controlador de tarefas:** O executor de controlador de tarefas é adequado para consultas complexas e de longa execução que exigem embaralhamento de dados em nós de trabalho e gerenciamento eficiente de recursos.
-   **em tempo real:** (preterido) atende a uma finalidade semelhante à do executor adaptável, mas é menos flexível e pode causar mais pressão de conexão em nós de trabalho.

Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador.

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus. \_ \_ \_ \_ nível de log de consulta de várias tarefas (enum) {#multi_task_logging}

Define um nível de log para qualquer consulta que gere mais de uma tarefa (isto é, que atinge mais de um fragmento). O registro em log é útil durante uma migração de aplicativo multilocatário, pois você pode escolher um erro ou avisar para essas consultas, para encontrá-las e adicionar um \_ filtro de ID de locatário a elas. Esse parâmetro pode ser definido em tempo de execução e é eficaz no coordenador. O valor padrão para esse parâmetro é \' off \' .

Os valores com suporte para essa enumeração são:

-   **desativada:** Desative o registro em log de todas as consultas que geram várias tarefas (ou seja, abrangem vários fragmentos)
-   **Depurar:** Registra a instrução no nível de severidade de depuração.
-   **log:** Registra a instrução no nível de severidade do LOG. A linha de log incluirá a consulta SQL que foi executada.
-   **AVISO:** Registra a instrução em nível de severidade de aviso.
-   **AVISO:** Registra a instrução no nível de severidade de aviso.
-   **erro:** Registra a instrução no nível de severidade do erro.

Pode ser útil usar `error` durante o teste de desenvolvimento e um nível de log inferior, como `log` durante a implantação de produção real.
Escolher fará `log` com que as consultas de várias tarefas apareçam nos logs de banco de dados com a própria consulta mostrada após a \" instrução.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

#### <a name="citusenable_repartition_joins-boolean"></a>citus. habilitar \_ \_ junções de repartição (booliano)

Normalmente, a tentativa de executar junções de repartição com o executor adaptável falhará com uma mensagem de erro.  No entanto, definir `citus.enable_repartition_joins` como true permite que o Citus (hiperescala) alterne temporariamente para o executor de controlador de tarefas para executar a junção.  O valor padrão é false.

### <a name="task-tracker-executor-configuration"></a>Configuração do executor do rastreador de tarefas

#### <a name="citustask_tracker_delay-integer"></a>citus. \_ \_ atraso do rastreador de tarefa (inteiro)

Esse parâmetro define o tempo de suspensão do controlador de tarefas entre os arredondas de gerenciamento de tarefas e os padrões para 200 ms. O processo do rastreador de tarefas é ativado regularmente, percorre todas as tarefas atribuídas a ele e agenda e executa essas tarefas.  Em seguida, o controlador de tarefas é suspenso por um período de tempo antes de passar essas tarefas novamente.
Esse valor de configuração determina o comprimento desse período de repouso. Esse parâmetro é eficaz nos trabalhos e precisa ser alterado no arquivo PostgreSQL. conf. Depois de editar o arquivo de configuração, os usuários podem enviar um sinal SIGHUP ou reiniciar o servidor para que a alteração entre em vigor.

Esse parâmetro pode ser reduzido para cortar o atraso causado devido ao executor do rastreador de tarefas, reduzindo a lacuna de tempo entre os arredondas de gerenciamento.
A diminuição do atraso é útil em casos em que as consultas de fragmento são curtas e, portanto, atualizam seu status regularmente.

#### <a name="citusmax_assign_task_batch_size-integer"></a>citus. Max \_ atribuir \_ \_ \_ tamanho do lote de tarefa (Integer)

O executor do controlador de tarefas no coordenador atribui as tarefas em lotes ao daemon nos trabalhadores de forma síncrona. Esse parâmetro define o número máximo de tarefas a serem atribuídas em um único lote. Escolher um tamanho de lote maior permite uma atribuição de tarefa mais rápida. No entanto, se o número de trabalhos for grande, poderá levar mais tempo para que todos os trabalhos obtenham tarefas.  Esse parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

#### <a name="citusmax_running_tasks_per_node-integer"></a>citus. Max \_ running \_ Tasks \_ por \_ nó (Integer)

O processo do controlador de tarefas agenda e executa as tarefas atribuídas a ele conforme apropriado. Esse valor de configuração define o número máximo de tarefas a serem executadas simultaneamente em um nó em um determinado momento e o padrão é 8.

O limite garante que você não \' tenha muitas tarefas atingindo o disco ao mesmo tempo e ajuda a evitar contenção de e/s de disco. Se suas consultas forem servidas da memória ou do SSDs, você poderá aumentar o máximo de \_ tarefas em execução \_ \_ por \_ nó sem muita preocupação.

#### <a name="cituspartition_buffer_size-integer"></a>citus. \_ \_ tamanho do buffer de partição (Integer)

Define o tamanho do buffer a ser usado para operações de partição e usa como padrão 8 MB.
O Citus (hiperescala) permite que os dados da tabela sejam reparticionados em vários arquivos quando duas tabelas grandes estão sendo Unidas. Depois que esse buffer de partição for preenchido, os dados reparticionados serão liberados para arquivos no disco.  Essa entrada de configuração pode ser definida em tempo de execução e entra em vigor nos trabalhos.

### <a name="explain-output"></a>Explicar a saída

#### <a name="citusexplain_all_tasks-boolean"></a>citus. explicar \_ todas as \_ tarefas (booliano)

Por padrão, o Citus (subscale) mostra a saída de uma única tarefa arbitrária ao executar [explicar](http://www.postgresql.org/docs/current/static/sql-explain.html) em uma consulta distribuída. Na maioria dos casos, a saída de explicação será semelhante entre as tarefas. Ocasionalmente, algumas das tarefas serão planejadas de maneira diferente ou terão tempos de execução muito maiores. Nesses casos, pode ser útil habilitar esse parâmetro, após o qual a saída de explicação incluirá todas as tarefas. A explicação de todas as tarefas pode fazer com que o explique demore mais.

## <a name="next-steps"></a>Próximas etapas

* Outra forma de configuração, além dos parâmetros de servidor, são as [Opções de configuração](concepts-hyperscale-configuration-options.md) de recurso em um grupo de servidores de hiperescala (Citus).
* A base de dados PostgreSQL subjacente também tem [parâmetros de configuração](http://www.postgresql.org/docs/current/static/runtime-config.html).
