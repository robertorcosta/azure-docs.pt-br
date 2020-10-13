---
title: Solucionar problemas de latência de replicação-banco de dados do Azure para MySQL
description: Saiba como solucionar problemas de latência de replicação com réplicas de leitura do banco de dados do Azure para MySQL
keywords: MySQL, solucionar problemas, latência de replicação em segundos
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876899"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Solucionar problemas de latência de replicação no banco de dados do Azure para MySQL

O recurso de [réplica de leitura](concepts-read-replicas.md) permite replicar dados de um servidor do Azure para MySQL para um servidor de réplica somente leitura. As réplicas de leitura são usadas para escalar horizontalmente a carga de trabalho ao rotear consultas de leitura/relatório do aplicativo para servidores de réplica. Isso reduz a pressão sobre o servidor primário e melhora o desempenho geral e a latência do aplicativo à medida que ele é dimensionado. As réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição do arquivo binário nativo (log binário) do mecanismo MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

O atraso de replicação nas réplicas de leitura secundárias depende do número de fatores, incluindo, entre outros 

- Latência da rede
- Volume da transação no servidor de origem
- Camada de computação de origem e servidor de réplica de leitura secundário
- Consultas em execução no servidor primário e secundário. 

Neste documento, você aprenderá como solucionar problemas de latência de replicação no banco de dados do Azure para MySQL. Além disso, você também entenderá algumas das causas comuns de maior latência de replicação em servidores de réplica.

## <a name="replication-concepts"></a>Conceitos de replicação

Quando o log binário está habilitado, o servidor de origem grava a transação confirmada no log binário, que é usado para replicação. O log binário é ativado por padrão para todos os servidores provisionados recentemente que dão suporte a até 16 TB de armazenamento. Em servidores de réplica, há dois threads em execução por servidor de réplica, um chamado thread de e/s e o outro chamado de thread do SQL.

- O **thread de e/s** conecta-se ao servidor de origem e solicita logs binários atualizados. Depois que esse thread recebe as atualizações de log binários, elas são salvas em um servidor de réplica, em um log local chamado log de retransmissão.
- O **thread SQL** lê o log de retransmissão e aplica a (s) alteração (ões) de dados em servidores de réplica.

## <a name="monitoring-replication-latency"></a>Monitorando latência de replicação

O banco de dados do Azure para MySQL fornece a métrica atraso de replicação em segundos no [Azure monitor](concepts-monitoring.md). Essa métrica está disponível somente em servidores de réplicas de leitura. Essa métrica é calculada usando a métrica de seconds_behind_master disponível no MySQL. Para entender a causa raiz da maior latência de replicação, conecte-se ao servidor de réplica usando o [MySQL Workbench](connect-workbench.md) ou o [Azure cloud Shell](https://shell.azure.com) e execute o seguinte comando:

 Substitua os valores pelo nome do servidor de réplica real e pelo nome de logon do usuário administrador. O nome de usuário do administrador requer ' @ \<servername> ' para o banco de dados do Azure para MySQL:

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Veja como é a experiência no terminal do Cloud Shell
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  No mesmo Azure Cloud Shell terminal, execute o comando a seguir

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  Uma saída típica terá A seguinte aparência:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitorando latência de replicação&quot;:::


A saída contém muitas informações, mas, normalmente, é importante se concentrar nas seguintes colunas:

|Métrica|Descrição|
|---|---|
|Slave_IO_State| Status atual do thread de e/s. Normalmente, o status é &quot;aguardando o mestre enviar evento&quot; se ele estiver sincronizando. No entanto, se você vir um status como &quot;conectando-se ao mestre&quot;, a réplica perderá a conexão com o servidor mestre. Verifique se o mestre está em execução ou se um firewall está bloqueando a conexão.|
|Master_Log_File| O arquivo de log binário no qual o mestre está gravando.|
|Read_Master_Log_Pos| Representa a posição no arquivo de log binário acima no qual o mestre está gravando.|
|Relay_Master_Log_File| Indicado representa o arquivo de log binário que o servidor de réplica está lendo do mestre.|
|Slave_IO_Running| Indica se o thread de e/s está em execução. Deve ser &quot;Sim&quot;. Se &quot;não&quot;, provavelmente a replicação será interrompida.|
|Slave_SQL_Running| Indica se o thread SQL está em execução. Deve ser &quot;Sim&quot;. Se &quot;não&quot;, provavelmente a replicação será interrompida.|
|Exec_Master_Log_Pos| Exibe a posição do Relay_Master_Log_File a réplica está sendo aplicada. Se houver latência, essa sequência de posição deverá ser menor que Read_Master_Log_Pos.|
|Relay_Log_Space|Exibe o limite superior do tamanho do log de retransmissão. Você pode verificar o tamanho consultando mostrar variáveis globais como &quot;relay_log_space_limit&quot;.|
|Seconds_Behind_Master| Exibe a latência de replicação em segundos.|
|Last_IO_Errno|Exibe o código de erro de thread de e/s, se houver. Para obter mais informações sobre esses códigos, consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_IO_Error| Exibe a mensagem de erro de thread de e/s, se houver.|
|Last_SQL_Errno|Exibe o código de erro de thread do SQL, se houver. Para obter mais informações sobre esses códigos, consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Exibe a mensagem de erro de thread do SQL, se houver.|
|Slave_SQL_Running_State| Indica o status atual do thread SQL. Observe que o &quot;bloqueio do sistema" mostrado nesse estado é um comportamento normal. É normal ver o status como "aguardando a confirmação da transação dependente". Isso indica que a réplica está aguardando que o mestre atualize as transações confirmadas.|

Se Slave_IO_Running for sim e Slave_SQL_Running for sim, a replicação estará funcionando corretamente. 

Em seguida, você precisa verificar Last_IO_Errno, Last_IO_Error, Last_SQL_Errno e Last_SQL_Error.  Esses campos contêm o número do erro e a mensagem de erro do erro mais recente que causou a interrupção do thread do SQL. Um número de erro 0 e uma mensagem vazia significam que não há erro. Um valor diferente de zero no erro deve ser investigado mais detalhadamente procurando o código de erro na [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Cenários comuns para alta latência de replicação

### <a name="network-latency-or-high-cpu-on-source-server"></a>Latência de rede ou alta CPU no servidor de origem

Se você observar os valores a seguir, a causa mais comum da latência de replicação será alta latência de rede ou alto consumo de CPU no servidor de origem. Nesse caso, o thread de e/s está em execução e aguardando o mestre. O mestre (servidor de origem) já foi gravado no arquivo de log binário #20, enquanto a réplica recebeu apenas até o arquivo #10. Os fatores principais que contribuem para alta latência de replicação nesse cenário são velocidade da rede ou alta utilização da CPU no servidor de origem.  No Azure, a latência de rede em uma região normalmente varia em milissegundos e toda a região pode ir até segundos. Na maioria dos casos, o atraso no thread de e/s para se conectar ao servidor de origem é causado devido à alta utilização da CPU no servidor de origem, fazendo com que o processamento do thread de e/s seja lento. Isso pode ser detectado monitorando a utilização da CPU e observando o número de conexões simultâneas no servidor de origem usando o Azure monitor.

Se você não vir a alta utilização da CPU no servidor de origem, as possíveis causas podem ser a latência de rede. Se você vir alta latência de rede anormalmente de repente, recomendamos que você verifique a [página de status do Azure](https://status.azure.com/status) para garantir que haja problemas ou interrupções não conhecidos. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>Intermitência pesada de transações no servidor de origem

Se você observar os valores a seguir, a causa mais comum da latência de replicação é a intermitência pesada de transações no servidor de origem. Na saída abaixo, embora a réplica possa recuperar o log binário por trás do mestre, o thread de e/s de réplica indica que o espaço de log de retransmissão já está cheio. Portanto, a velocidade da rede não está causando o atraso, porque a réplica já estava tentando se acumular o mais rápido possível. Em vez disso, o tamanho do log binário atualizado excede o limite superior do espaço de log de retransmissão. Para solucionar esse problema ainda mais, o [log de consultas lentos](concepts-server-logs.md) deve ser habilitado no servidor mestre. Logs de consulta lentos permitem que você identifique transações de longa execução no servidor de origem. As consultas identificadas precisam ser ajustadas para reduzir a latência no servidor. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

A seguir estão as causas comuns da latência nesta categoria:

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>Latência de replicação devido a carregamento de dados no servidor de origem
Em alguns casos, há um carregamento de dados semanal ou mensal em servidores de origem. Infelizmente, a latência de replicação é inevitável nesse caso. Nesse cenário, os servidores de réplica eventualmente são atualizados após a conclusão do carregamento de dados no servidor de origem.


### <a name="slowness-on-the-replica-server"></a>Lentidão no servidor de réplica

Se você observar os valores a seguir, a causa mais comum pode ser um problema no servidor de réplica que precisa de mais investigação. Nesse cenário, como visto na saída, os threads de e/s e SQL estão funcionando bem e a réplica está lendo o mesmo arquivo de log binário que as gravações mestras. No entanto, ocorre alguma latência no servidor de réplica para refletir a mesma transação do servidor de origem. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

A seguir estão as causas comuns da latência nesta categoria:

#### <a name="no-primary-or-unique-key-on-a-table"></a>Nenhuma chave primária ou exclusiva em uma tabela

O banco de dados do Azure para MySQL usa replicação baseada em linha. Com a replicação baseada em linha, o servidor mestre grava eventos no log binário sobre alteração de linha de tabela individual. O thread do SQL, por sua vez, executa essas alterações nas linhas da tabela correspondentes no servidor de réplica. Nenhuma chave primária ou exclusiva em uma tabela é uma das causas comuns da latência de replicação. A falta de chaves primárias ou exclusivas leva à verificação de todas as linhas na tabela de destino pelo thread do SQL para aplicar as alterações.

No MySQL, a chave primária é um índice associado que garante um desempenho de consulta rápido, pois não pode incluir valores nulos. Com o mecanismo de armazenamento do InnoDB, os dados da tabela são organizados fisicamente para fazer pesquisas e classificações extremamente rápidas com base na chave primária. Portanto, é recomendável adicionar uma chave primária em tabelas no servidor de origem antes de criar o servidor de réplica. Nesse cenário, você precisa adicionar chaves primárias no servidor de origem e recriar réplicas de leitura para ajudar a melhorar a latência de replicação.

Você pode usar a seguinte consulta para determinar as tabelas com a chave primária ausente no servidor de origem:

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>Latência de replicação devido a consultas de longa execução no servidor de réplica

É possível que a carga de trabalho no servidor de réplica possa impedir que o thread do SQL seja mantido com o thread de e/s. Essa é uma das causas comuns da alta latência de replicação se houver uma consulta de execução longa no servidor de réplica. Nesse caso, o [log de consulta lento](concepts-server-logs.md) deve ser habilitado no servidor de réplica para ajudar a solucionar o problema. Consultas lentas podem aumentar os consumos de recursos ou reduzir o servidor, portanto, a réplica não será capaz de fazer a atualização com o mestre. Nesse cenário, você precisa ajustar consultas lentas. Consultas mais rápidas impedem o bloqueio de thread SQL e melhora significativamente a latência de replicação.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>Latência de replicação devido a consultas DDL no servidor de origem
Se houver um comando DDL de longa execução, como [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) executado no servidor de origem, e digamos que levou 1 hora para ser executada. Durante esse tempo, pode haver milhares de outras consultas em execução em paralelo no servidor de origem. Quando o DDL é replicado para a réplica, para garantir a consistência do banco de dados, o mecanismo MySQL precisa executar o DDL em um único thread de replicação. Portanto, todas as outras consultas replicadas serão bloqueadas e precisarão aguardar por uma hora ou mais até que a operação DDL seja concluída no servidor de réplica. Isso é verdadeiro independentemente da operação DDL online ou não. Com as operações DDL, espera-se que a replicação Veja uma latência de replicação maior.

Se você tiver o [log de consulta lento](concepts-server-logs.md) habilitado no servidor de origem, esse cenário poderá ser detectado examinando os logs de consulta lentos para ver se um comando DDL foi executado no servidor de origem. Embora a remoção de índice, a renomeação e a criação devem usar o algoritmo de local para a tabela ALTER TABLE, ela pode envolver a cópia de dados da tabela e a recompilação da tabela. Normalmente, para o algoritmo local, não há suporte para DML simultânea, mas um bloqueio de metadados exclusivo na tabela pode ser resumido durante as fases de preparação e execução da operação. Dessa forma, para a instrução CREATE INDEX, o algoritmo de cláusulas e o bloqueio podem ser usados para influenciar o método de cópia de tabela e o nível de simultaneidade para leitura e gravação, ao mesmo assim que a adição de um índice de texto completo ou espacial ainda impedirá operações DML. Veja abaixo um exemplo de como criar um índice com as cláusulas de algoritmo e de bloqueio:

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Infelizmente, para a instrução DDL que exige um bloqueio, a latência de replicação não pode ser evitada. em vez disso, esses tipos de operações DDL devem ser executados fora do horário de pico, por exemplo, durante a noite e para reduzir o impacto em potencial.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>Latência de replicação devido à SKU inferior do servidor de réplica

No banco de dados do Azure para MySQL, as réplicas de leitura são criadas com a mesma configuração de servidor que o servidor mestre. A configuração do servidor de réplica pode ser alterada depois de criada. No entanto, se o servidor de réplica for desatualizado, a carga de trabalho poderá causar um maior consumo de recursos que, por sua vez, pode levar à latência de replicação. Isso pode ser observado monitorando o consumo de CPU e de memória do servidor de réplica do Azure Monitor. Nesse cenário, é recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores do que a origem para garantir que a réplica seja capaz de acompanhar o mestre.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>Melhorando a latência de replicação usando o ajuste de parâmetro do servidor no servidor de origem

No banco de dados do Azure para MySQL, a replicação é otimizada para ser executada com threads paralelos em réplicas por padrão. Para cargas de trabalho de simultaneidade alta no servidor de origem em que o servidor de réplica está falhando na atualização, a latência de replicação pode melhorar com a configuração do parâmetro binlog_group_commit_sync_delay no servidor de origem. Esse parâmetro controla o número de microssegundos que a confirmação de log binário aguarda antes de sincronizar o arquivo de log binário. O benefício é que, em vez de aplicar imediatamente cada transação confirmada, o mestre envia as atualizações de log binários em massa. Isso reduz a e/s na réplica e ajuda a melhorar o desempenho. Nesse cenário, pode ser útil definir binlog_group_commit_sync_delay como 1000 ou assim e monitorar a latência de replicação. Esse parâmetro deve ser definido com cuidado e aproveitado somente para cargas de trabalho simultâneas altas. Para um cenário de simultaneidade baixa com muitas transações singleton, a configuração binlog_group_commit_sync_delay pode adicionar à latência porque o thread de e/s está aguardando atualizações de log binário em massa, enquanto apenas algumas transações podem ser confirmadas. 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a [visão geral da replicação binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
