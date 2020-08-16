---
title: Práticas recomendadas de desenvolvimento de aplicativo-banco de dados do Azure para MySQL
description: Saiba mais sobre as práticas recomendadas ao criar um aplicativo com o banco de dados do Azure para MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259240"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>Práticas recomendadas para a criação de aplicativos com o banco de dados do Azure para MySQL 

Aqui estão algumas práticas recomendadas para ajudar a criar aplicativos prontos para a nuvem usando o banco de dados do Azure para MySQL, que pode reduzir o tempo de desenvolvimento para seu aplicativo. 

## <a name="application-and-database-resource-configuration"></a>Configuração de recurso de aplicativo e banco de dados

### <a name="application-and-database-in-the-same-region"></a>Aplicativo e banco de dados na mesma região
Verifique se **todas as suas dependências estão na mesma região**  ao implantar seu aplicativo no Azure. A difusão de instâncias entre regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo. 

### <a name="keep-your-mysql-server-secure"></a>Mantenha seu servidor MySQL seguro
Seu servidor MySQL deve ser configurado para ser [seguro](https://docs.microsoft.com/azure/mysql/concepts-security) e não acessível publicamente. Use qualquer uma dessas opções para proteger seu servidor: 
- [Regras de firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) ou
- [Redes virtuais](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) ou 
- [Link privado](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

Para segurança, você deve sempre se conectar ao servidor MySQL por **SSL** e configurar seu servidor MySQL e seu aplicativo para usar o **TLS 1.2**. Consulte [como configurar SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Ajustar os parâmetros do servidor
Para cargas de trabalho com uso intenso de leitura que ajustam esses parâmetros, ' tmp_table_size e max_heap_table_size ' podem ajudar a otimizar o melhor desempenho. Para calcular os valores necessários para tmp_table_size e max_heap_table_size, examine o total de valores de memória por conexão e a memória base. A soma dos parâmetros de memória por conexão, exceto tmp_table_size, combinada com as contas de memória base para a memória total do servidor.

Para calcular o maior tamanho possível de tmp_table_size e max_heap_table_size, use a seguinte fórmula:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Memória total indica a quantidade total de memória que o servidor tem em todo o vCores provisionado.  Por exemplo, em um servidor de banco de dados do Azure para MySQL Uso Geral 2 vCore, a memória total será de 5 GB * 2.  Mais detalhes sobre a memória de cada camada podem ser encontrados na documentação do [tipo de preço](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) .
> A memória base indica as variáveis de memória, como query_cache_size e innodb_buffer_pool_size, o MySQL será inicializado e alocado na inicialização do servidor.  A memória por conexão, como sort_buffer_size e join_buffer_size, é a memória que é alocada somente quando uma consulta a exige.

### <a name="create-a-non-admin-user"></a>Criar um usuário não administrador 
[Crie usuários não administradores](https://docs.microsoft.com/azure/mysql/howto-create-users) para cada um dos bancos de dados. Normalmente, os nomes de usuário são identificados como os nomes de BD.

### <a name="resetting-your-password"></a>Redefinindo sua senha
Você pode [redefinir sua senha](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) para o servidor MySQL usando portal do Azure. 

Redefinir a senha do servidor para um banco de dados de produção pode desativar seu aplicativo. É um bom padrão para redefinir a senha para qualquer carga de trabalho de produção em horários de pico para minimizar o impacto para os usuários finais do aplicativo.

## <a name="performance-and-resiliency"></a>Desempenho e resiliência 
Aqui estão algumas ferramentas e padrões que você pode usar para ajudar a depurar problemas de desempenho com seu aplicativo.

### <a name="enable-slow-query-logs-identify-performance-issues"></a>Habilitar logs de consulta lentos identificar problemas de desempenho
Você pode habilitar [logs de consulta lentos](https://docs.microsoft.com/azure/mysql/concepts-server-logs) e [logs de auditoria](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) em seu servidor. Analisar logs de consulta lentos pode ajudar a identificar gargalos de desempenho para solução de problemas. 

Os logs de auditoria também estão disponíveis por meio dos logs de diagnóstico do Azure em Azure Monitor logs, hubs de eventos e conta de armazenamento. Consulte [como solucionar problemas de desempenho de consulta](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>Usar o pool de conexões
O gerenciamento de conexões de banco de dados pode ter um impacto significativo no desempenho do aplicativo como um todo. Para otimizar o desempenho, você deve reduzir o número de vezes que as conexões são estabelecidas e o tempo para estabelecer conexões em caminhos de código-chave.  Use o [pool de conexões](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) para se conectar ao banco de dados do Azure para MySQL para melhorar a resiliência e o desempenho. 

[ProxySQL](https://proxysql.com/), que é um pooler de conexão, pode ser usado com eficiência para gerenciar conexões. O uso de um pool de conexões pode diminuir as conexões ociosas e reutilizar conexões existentes ajudará a evitar problemas. Consulte [como configurar o ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) para saber mais. 

### <a name="retry-logic-to-handle-transient-errors"></a>Lógica de repetição para manipular erros transitórios
Seu aplicativo pode apresentar [erros transitórios](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) em que as conexões com o banco de dados estão sendo descartadas ou perdidas intermitentemente. Nessas situações, o servidor está em execução depois de uma a duas tentativas em 5-10 segundos. 

Um bom padrão a seguir com Retry é aguardar 5 segundos antes da primeira tentativa e depois seguir cada repetição aumentando a espera gradualmente até 60 segundos. Limite o número máximo de tentativas em que o aplicativo considera a operação com falha, para que você possa investigar ainda mais. Consulte [como solucionar problemas de erros de conexão](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) para saber mais. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Habilitar a replicação de leitura para mitigar failovers
Você pode usar [a replicação de dados em](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) cenários de failover. Ao usar réplicas de leitura, nenhum failover automatizado entre os servidores mestre e de réplica ocorre. Você perceberá um atraso entre o mestre e a réplica, uma vez que a replicação é assíncrona. O retardo de rede pode ser influenciado por vários fatores, como a quantidade de carga de trabalho em execução no servidor mestre e a latência entre os data centers. Na maioria dos casos, a latência de réplica varia entre alguns segundos e alguns minutos.

## <a name="database-deployment"></a>Implantação de banco de dados 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Configurar a tarefa banco de dados do Azure para MySQL em seu pipeline de implantação de CI/CD
Ocasionalmente, você precisaria implantar alterações em seu banco de dados. Nesses casos, você pode criar usar CI (integração contínua) e o CD (entrega contínua) por meio de [pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/) e usar uma tarefa para [o servidor MySQL](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) para atualizar o banco de dados executando um script personalizado em seu banco de dados.

### <a name="manual-database-deployment"></a>Implantação manual de banco de dados 
Durante a implantação manual de banco de dados, aqui está um bom padrão a ser seguido para minimizar o tempo de inatividade ou reduzir o risco de falha na implantação: 

1. Criar uma cópia do banco de dados de produção em um novo banco de dados usando o [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) ou o [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html) 
2. Atualize o novo banco de dados com suas novas alterações de esquema ou atualizações necessárias para seu banco de dados. 
3. Coloque o banco de dados de produção no estado somente leitura. Você não deve ter operações de gravação no banco de dados de produção até que a implantação seja concluída. 
4. Teste seu aplicativo com o banco de dados recentemente atualizado da etapa 1.
5. Implante as alterações do aplicativo e verifique se o aplicativo agora está usando o novo banco de dados que tem as atualizações mais recentes. 
6. Mantenha o banco de dados de produção antigo para que você possa reverter as alterações. Em seguida, você pode avaliar para excluir o banco de dados de produção antigo ou exportá-lo no armazenamento do Azure, se necessário. 

>[!NOTE]
>  - Se o aplicativo for como um aplicativo de comércio eletrônico em que você não consiga colocá-lo no estado somente leitura, implante as alterações diretamente no banco de dados de produção depois de fazer um backup.  A alteração de alterações deve ocorrer durante o horário de pico, com baixo tráfego para o aplicativo para minimze o impacto, pois alguns usuários podem experimentar solicitações com falha. 
>  - Verifique se o código do aplicativo também lida com as solicitações com falha.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Use métricas nativas do MySQL para ver se sua carga de trabalho está excedendo tamanhos de tabela temporária na memória
Com uma carga de trabalho com uso intenso de leitura, as consultas em execução no servidor MySQL podem exceder os tamanhos de tabela temporária na memória. Ele pode fazer com que o servidor mude para gravar tabelas temporárias em disco, o que afeta o desempenho do seu aplicativo. Para determinar se o servidor está gravando no disco como resultado da excedente do tamanho de tabela temporário, examine as seguintes métricas:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
A métrica de created_tmp_disk_tables indica quantas tabelas foram criadas no disco, enquanto a métrica de created_tmp_table informa quantas tabelas temporárias devem ser formadas na memória, considerando sua carga de trabalho. Para determinar se a execução de uma consulta específica usará tabelas temporárias, execute explicar na consulta. Os detalhes na coluna ' extra ' indicarão ' usando temporário ' se a consulta for executada usando tabelas temporárias.

Para calcular a porcentagem de sua carga de trabalho com consultas que são despejadas em discos, use os valores de métrica na fórmula abaixo:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Idealmente, esse percentual deve ser inferior a 25%. Se você perceber que a porcentagem é 25% ou maior, sugerimos modificar dois parâmetros de servidor, tmp_table_size e max_heap_table_si


## <a name="database-schema-and-queries"></a>Esquema e consultas de banco de dados

Aqui estão algumas dicas e truques para ter em mente quando você cria seu esquema de banco de dados e suas consultas.

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>Sempre usar o tipo de dados correto para as colunas da tabela
O uso dos tipos de dados corretos com base no tipo de dado que você deseja armazenar pode otimizar o armazenamento e reduzir os erros que podem ocorrer devido a tipos incorretos.

### <a name="use-indexes"></a>Usar índices
Para evitar consultas lentas, você pode usar índices. Os índices podem ajudar a localizar linhas com colunas específicas rapidamente. Consulte [como usar índices no MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="explain-your-select-queries"></a>Explique suas consultas SELECT
Use a [explicação](https://dev.mysql.com/doc/refman/8.0/en/explain.html) para obter informações sobre o que o MySQL está fazendo para executar sua consulta. Isso pode ajudá-lo a detectar gargalos ou problemas com sua consulta. Consulte [como usar explicar para analisar o desempenho da consulta](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).


