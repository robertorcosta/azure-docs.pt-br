---
title: Loja de Consulta - Banco de Dados Azure para MySQL
description: Saiba mais sobre o recurso Query Store no Azure Database for MySQL para ajudá-lo a acompanhar o desempenho ao longo do tempo.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d138c2fb8ed667d5b3c961c9f567264fa40edaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537033"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitore o banco de dados do Azure para o desempenho do MySQL com a Query Store

**Aplica-se a:** Banco de dados Azure para MySQL 5.7

O recurso Query Store no Azure Database for MySQL fornece uma maneira de acompanhar o desempenho da consulta ao longo do tempo. O Repositório de Consultas simplifica a solução de problemas ajudando você a rapidamente localizar as consultas de execução mais longa e que consomem mais recursos. O Repositório de Consultas captura automaticamente um histórico das estatísticas de runtime e consultas e o retém para sua análise. Ele separa os dados por janelas de tempo para que você possa ver padrões de uso do banco de dados. Os dados para todos os usuários, bancos de dados e consultas são armazenados no banco de dados do esquema **mysql** na instância Do Zure Database for MySQL.

## <a name="common-scenarios-for-using-query-store"></a>Cenários comuns para usar o Query Store

A loja de consultas pode ser usada em vários cenários, incluindo o seguinte:

- Detectando consultas regredidas
- Determinação do número de vezes que uma consulta foi executada em uma determinada janela de tempo
- Comparar o tempo médio de execução de uma consulta entre janelas de tempo para ver grandes deltas

## <a name="enabling-query-store"></a>Habilitando o Repositório de Consultas

O Repositório de Consultas é um recurso que requer aceitação, portanto, ele não está ativo em um servidor por padrão. A loja de consultas está ativada ou desativada globalmente para todos os bancos de dados em um determinado servidor e não pode ser ativada ou desativada por banco de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Habilitar o Repositório de Consultas usando o portal do Azure

1. Faça login no portal Azure e selecione seu banco de dados Azure para o servidor MySQL.
1. Selecione **Parâmetros de Servidor** na seção **Configurações** do menu.
1. Procure o parâmetro query_store_capture_mode.
1. Defina o valor para ALL e **Salve**.

Para habilitar estatísticas de espera em sua loja de consultas:

1. Procure o parâmetro query_store_wait_sampling_capture_mode.
1. Defina o valor para ALL e **Salve**.

Aguarde até 20 minutos para que o primeiro lote de dados persista no banco de dados mysql.

## <a name="information-in-query-store"></a>Informações no Repositório de Consultas

O Repositório de Consultas tem dois repositórios:

- Uma loja de estatísticas em tempo de execução para persistir as informações das estatísticas de execução de consulta.
- Uma reserva de estatísticas de espera para informações de estatísticas de espera persistentes.

Para minimizar o uso do espaço, as estatísticas de execução em tempo de execução no armazenamento de estatísticas de tempo de execução são agregadas em uma janela de tempo fixa e configurável. As informações nesses repositórios são visíveis consultando as exibições do repositório de consultas.

A consulta a seguir retorna informações sobre consultas no Repositório de Consultas:

```sql
SELECT * FROM mysql.query_store;
```

Ou esta consulta para estatísticas de espera:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Localizando consultas de espera

> [!NOTE]
> As estatísticas de espera não devem ser ativadas durante o horário de pico de carga de trabalho ou ser ativadas indefinidamente para cargas de trabalho sensíveis. <br>Para cargas de trabalho em execução com alta utilização da CPU ou em servidores configurados com vCores inferiores, tenha cuidado ao ativar estatísticas de espera. Não deve ser ligado indefinidamente. 

Os tipos de evento de espera combinam diferentes eventos de espera em buckets por semelhança. O Repositório de Consultas fornece o tipo de evento de espera, o nome do evento de espera específico e a consulta em questão. Ser capaz de correlacionar essas informações de espera com as estatísticas de runtime de consulta significa que você pode obter uma compreensão mais profunda do que contribui para as características de desempenho de consulta.

Aqui estão alguns exemplos de como você pode obter mais insights sobre sua carga de trabalho usando as estatísticas de espera no Repositório de Consultas:

| **Observação** | **Ação** |
|---|---|
|Esperas de bloqueio alto | Verifique os textos de consulta para as consultas afetadas e identifique as entidades de destino. Procure no Repositório de Consultas outras consultas que modificam a mesma entidade, que é executada com frequência e/ou têm alta duração. Depois de identificar essas consultas, considere alterar a lógica do aplicativo para melhorar a simultaneidade ou use um nível de isolamento menos restritivo. |
|Esperas de E/S de buffer alto | Localize as consultas com um grande número de leituras físicas no Repositório de Consultas. Se eles coincidirem com as consultas com altas esperas de IO, considere introduzir um índice na entidade subjacente, para fazer buscas em vez de varreduras. Isso minimizaria a sobrecarga de E/S das consultas. Verifique as **Recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice para esse servidor que otimizariam as consultas. |
|Esperas de memória alta | Localize as consultas que consomem mais memória no Repositório de Consultas. Essas consultas estão provavelmente atrasando o andamento das consultas afetadas. Verifique as **Recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice que otimizariam essas consultas.|

## <a name="configuration-options"></a>Opções de configuração

Quando o Repositório de Consultas está habilitado, ele salva dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela.

As opções a seguir estão disponíveis para configurar os parâmetros do Repositório de Consultas.

| **Parâmetro** | **Descrição** | **Padrão** | **Gama** |
|---|---|---|---|
| query_store_capture_mode | Gire o recurso de loja de consultas ON/OFF com base no valor. Nota: Se performance_schema estiver DESLIGADO, ligar query_store_capture_mode ligará performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para este recurso. | ALL | NENHUM, TODOS |
| query_store_capture_interval | O intervalo de captura da loja de consulta em minutos. Permite especificar o intervalo em que as métricas de consulta são agregadas | 15 | 5 - 60 |
| query_store_capture_utility_queries | Ligando ou desligando para capturar todas as consultas de utilidade que está sendo executadas no sistema. | Não | SIM, NÃO, NÃO. |
| query_store_retention_period_in_days | Janela de tempo em dias para reter os dados no armazenamento de consultas. | 7 | 1 a 30 |

As opções a seguir se aplicam especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Padrão** | **Gama** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Permite ligar /desligar as estatísticas de espera. | Nenhuma | NENHUM, TODOS |
| query_store_wait_sampling_frequency | Altera a freqüência de amostragem de espera em segundos. 5 a 300 segundos. | 30 | 5-300 |

> [!NOTE]
> **Atualmente, query_store_capture_mode** substitui essa configuração, o que significa que **tanto query_store_capture_mode** quanto **query_store_wait_sampling_capture_mode** têm que ser habilitados para ALL para que as estatísticas de espera funcionem. Se **query_store_capture_mode** estiver desligado, as estatísticas de espera também são desligadas, uma vez que as estatísticas de espera utilizam o performance_schema ativado e o query_text capturado pela loja de consultas.

Use o  [portal Azure](howto-server-parameters.md)ou [a Cli do Azure](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Exibições e funções

Exiba e gerencie o Repositório de Consultas usando as seguintes exibições e funções. Qualquer pessoa na [função pública de privilégio seleto](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) pode usar essas exibições para ver os dados no Query Store. Essas visualizações só estão disponíveis no banco de dados **mysql.**

Consultas são normalizadas examinando sua estrutura após a remoção de literais e constantes. Se duas consultas forem idênticas, exceto por valores literais, elas terão o mesmo hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Essa exibição retorna todos os dados no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário e ID de consulta distinta.

| **Nome** | **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `schema_name`| varchar(64) | Não | Nome do esquema |
| `query_id`| bigint(20) | Não| ID exclusivo gerado para a consulta específica, se a mesma consulta for executada em esquemadiferente, um novo ID será gerado |
| `timestamp_id` |  timestamp| Não| Carimbo de tempo no qual a consulta é executada. Isso é baseado na configuração query_store_interval|
| `query_digest_text`| longtext| Não| O texto de consulta normalizado após a remoção de todos os literais|
| `query_sample_text` | longtext| Não| Primeira aparição da consulta real com literais|
| `query_digest_truncated` | bit| YES| Se o texto de consulta foi truncado. O valor será Sim se a consulta for maior que 1 KB|
| `execution_count` | bigint(20)| Não| O número de vezes que a consulta foi executada para este ID de carimbo de tempo / durante o período de intervalo configurado|
| `warning_count` | bigint(20)| Não| Número de avisos que esta consulta gerou durante o interno|
| `error_count` | bigint(20)| Não| Número de erros que essa consulta gerou durante o intervalo|
| `sum_timer_wait` | double| YES| Tempo total de execução desta consulta durante o intervalo|
| `avg_timer_wait` | double| YES| Tempo médio de execução para esta consulta durante o intervalo|
| `min_timer_wait` | double| YES| Tempo mínimo de execução para esta consulta|
| `max_timer_wait` | double| YES| Tempo máximo de execução|
| `sum_lock_time` | bigint(20)| Não| Quantidade total de tempo gasto para todas as fechaduras para esta execução de consulta durante esta janela de tempo|
| `sum_rows_affected` | bigint(20)| Não| Número de linhas afetadas|
| `sum_rows_sent` | bigint(20)| Não| Número de linhas enviadas ao cliente|
| `sum_rows_examined` | bigint(20)| Não| Número de linhas verificadas|
| `sum_select_full_join` | bigint(20)| Não| Número de adesões completas|
| `sum_select_scan` | bigint(20)| Não| Número de varreduras selecionadas |
| `sum_sort_rows` | bigint(20)| Não| Número de linhas classificadas|
| `sum_no_index_used` | bigint(20)| Não| Número de vezes em que a consulta não usou nenhum índice|
| `sum_no_good_index_used` | bigint(20)| Não| Número de vezes em que o mecanismo de execução de consulta não usou nenhum bom índice|
| `sum_created_tmp_tables` | bigint(20)| Não| Número total de tabelas temporárias criadas|
| `sum_created_tmp_disk_tables` | bigint(20)| Não| Número total de tabelas temporárias criadas em disco (gera I/O)|
| `first_seen` |  timestamp| Não| A primeira ocorrência (UTC) da consulta durante a janela de agregação|
| `last_seen` |  timestamp| Não| A última ocorrência (UTC) da consulta durante esta janela de agregação|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Essa exibição retorna os dados de eventos de espera no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário, ID de consulta e evento distinto.

| **Nome**| **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `interval_start` |  timestamp | Não| Início do intervalo (incremento de 15 minutos)|
| `interval_end` |  timestamp | Não| Fim do intervalo (incremento de 15 minutos)|
| `query_id` | bigint(20) | Não| Gerou id exclusivo na consulta normalizada (do ponto de consulta)|
| `query_digest_id` | varchar(32) | Não| O texto de consulta normalizado após a remoção de todos os literais (do depósito de consultas) |
| `query_digest_text` | longtext | Não| Primeira aparição da consulta real com literais (da loja de consultas) |
| `event_type` | varchar(32) | Não| Categoria do evento de espera |
| `event_name` | varchar(128) | Não| Nome do evento de espera |
| `count_star` | bigint(20) | Não| Número de eventos de espera amostrados durante o intervalo para a consulta |
| `sum_timer_wait_ms` | double | Não| Tempo total de espera (em milissegundos) desta consulta durante o intervalo |

### <a name="functions"></a>Funções

| **Nome**| **Descrição** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Expurga todos os dados do armazenamento de consulta antes do carimbo de hora dado |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Expurga todos os dados do evento de espera antes do carimbo de hora dado |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Expurga recomendações cuja expiração é antes do carimbo de tempo dado |

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- Se um servidor MySQL `default_transaction_read_only` estiver ligado, o Query Store não poderá capturar dados.
- A funcionalidade do Query Store pode ser interrompida se\>encontrar consultas unicode longas ( = 6000 bytes).
- O período de retenção das estatísticas de espera é de 24 horas.
- As estatísticas de espera usam a amostra para capturar uma fração de eventos. A freqüência pode ser `query_store_wait_sampling_frequency`modificada usando o parâmetro .

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Query Performance Insights](concepts-query-performance-insight.md)
