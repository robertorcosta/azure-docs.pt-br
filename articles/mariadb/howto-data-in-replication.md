---
title: Configurar a replicação de dados - Banco de dados Azure para MariaDB
description: Este artigo descreve como configurar a replicação de dados no Banco de Dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 51b800dde140affd222f2bdb341c0fbf3a57d8cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530148"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configure a replicação de dados no banco de dados Do zure para MariaDB

Este artigo descreve como configurar a replicação de dados no Banco de Dados do Azure para MariaDB, configurando os servidores mestre e de réplica. Este artigo pressupõe que você tenha alguma experiência prévia com servidores e bancos de dados Do MariaDB.

Para criar uma réplica no banco de dados do Azure para o serviço MariaDB, a Replicação de Dados sincroniza dados de um servidor Mestre MariaDB no local, em máquinas virtuais (VMs) ou em serviços de banco de dados em nuvem.

> [!NOTE]
> Se o seu servidor mestre for a versão 10.2 ou mais recente, recomendamos que você configure a Replicação de Dados usando [o ID de transação global](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Crie um servidor MariaDB para usar como uma réplica

1. Crie um novo banco de dados Azure para servidor MariaDB (por exemplo, replica.mariadb.database.azure.com). O servidor é o servidor de réplica na replicação de dados.

    Para saber mais sobre a criação do servidor, consulte [Criar um banco de dados Azure para servidor MariaDB usando o portal Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Você deve criar o banco de dados Azure para servidor MariaDB nos níveis de preços de propósito geral ou otimizado de memória.

2. Crie contas de usuário idênticas e privilégios correspondentes.
    
    As contas de usuário não são replicadas do servidor mestre para o servidor de réplicas. Para fornecer acesso ao servidor de réplica, você deve criar manualmente todas as contas e privilégios correspondentes no recém-criado Banco de Dados Azure para o servidor MariaDB.

## <a name="configure-the-master-server"></a>Configurar o servidor mestre

As etapas a seguir preparam e configuram o servidor MariaDB hospedado no local, em uma VM ou em um serviço de banco de dados em nuvem para replicação de dados. O servidor MariaDB é o mestre em Replicação de Dados.

1. Ligue o registro binário.
    
    Para ver se o registro binário está habilitado no mestre, digite o seguinte comando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) variável `ON`retornar o valor, o registro binário será ativado no servidor.

   Se `log_bin` devolver `OFF`o valor, edite o arquivo `log_bin=ON` **my.cnf** de modo que se acessa o registro binário. Reinicie o servidor para fazer com que a alteração faça efeito.

2. Configure as configurações do servidor mestre.

    A replicação de dados `lower_case_table_names` requer que o parâmetro seja consistente entre os servidores mestre e réplica. O `lower_case_table_names` parâmetro é `1` definido como padrão no Banco de Dados Azure para MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Crie uma nova função de replicação e configure permissões.

   Crie uma conta de usuário no servidor mestre configurada com privilégios de replicação. Você pode criar uma conta usando comandos SQL ou MySQL Workbench. Se você planeja replicar com SSL, você deve especificar isso ao criar a conta de usuário.
   
   Para saber como adicionar contas de usuário em seu servidor mestre, consulte a [documentação DoMariaDB](https://mariadb.com/kb/en/library/create-user/).

   Usando os seguintes comandos, a nova função de replicação pode acessar o mestre a partir de qualquer máquina, não apenas a máquina que hospeda o próprio mestre. Para esse acesso, **especifique\@'%'** do usuário no comando para criar um usuário.
   
   Para saber mais sobre a documentação do MariaDB, [consulte especificar nomes de contas](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Comando SQL**

   - Replicação com SSL

       Para exigir SSL para todas as conexões do usuário, digite o seguinte comando para criar um usuário:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicação sem SSL

       Se o SSL não for necessário para todas as conexões, digite o seguinte comando para criar um usuário:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **Workbench do MySQL**

   Para criar a função de replicação na bancada do MySQL, no painel **Gerenciamento,** selecione **Usuários e Privilégios**. Em seguida, **selecione Adicionar conta**.
 
   ![Privilégios e usuários](./media/howto-data-in-replication/users_privileges.png)

   Digite um nome de usuário no campo **Nome de login.**

   ![Sincronizar usuário](./media/howto-data-in-replication/syncuser.png)
 
   Selecione o painel **Funções Administrativas** e, em seguida, na lista de **Privilégios Globais,** selecione **Escravo de replicação**. Selecione **Aplicar** para criar a função de replicação.

   ![Replicação subordinada](./media/howto-data-in-replication/replicationslave.png)


4. Defina o servidor mestre para o modo somente leitura.

   Antes de despejar um banco de dados, o servidor deve ser colocado no modo somente leitura. Enquanto estiver no modo somente leitura, o mestre não pode processar nenhuma transação de gravação. Para ajudar a evitar o impacto nos negócios, agende a janela somente leitura durante um horário fora do pico.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obtenha o nome e deslocamento do arquivo de log binário atual.

   Para determinar o nome e deslocamento do arquivo [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)de log binário atual, execute o comando .
    
   ```sql
   show master status;
   ```
   Os resultados devem ser semelhantes à tabela a seguir:
   
   ![Resultados de status do mestre](./media/howto-data-in-replication/masterstatus.png)

   Observe o nome do arquivo binário, porque ele será usado em etapas posteriores.
   
6. Obtenha a posição GTID (opcional, necessária para a replicação com GTID).

   Execute a [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) função para obter a posição GTID para o nome do arquivo binlog correspondente e deslocamento.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Despeje e restaure o servidor mestre

1. Despeje todos os bancos de dados do servidor principal.

   Use mysqldump para despejar todos os bancos de dados do servidor mestre. Não é necessário despejar a biblioteca MySQL e a biblioteca de testes.

    Para obter mais informações, consulte [Dump e restore](howto-migrate-dump-restore.md).

2. Defina o servidor mestre para o modo de leitura/gravação.

   Depois que o banco de dados for despejado, altere o servidor Mestre MariaDB de volta para o modo de leitura/gravação.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaure o arquivo de despejo para o novo servidor.

   Restaure o arquivo de despejo para o servidor criado no banco de dados do Azure para o serviço MariaDB. Consulte [Dump & Restore](howto-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo em um servidor MariaDB.

   Se o arquivo de despejo for grande, carregue-o para uma VM no Azure na mesma região do servidor de réplica. Restaure-o no banco de dados Do Zure para o servidor MariaDB da VM.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Vincule os servidores master e replica para iniciar a replicação de dados

1. Defina o servidor mestre.

   Todas as funções de replicação nos dados são feitas por procedimentos armazenados. Você pode encontrar todos os procedimentos em [Procedimentos armazenados de replicação nos dados](reference-data-in-stored-procedures.md). Os procedimentos armazenados podem ser executados no shell MySQL ou MySQL Workbench.

   Para vincular dois servidores e iniciar a replicação, faça login no servidor de réplica de destino no Azure DB para o serviço MariaDB. Em seguida, defina a instância externa `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` como o servidor mestre usando o procedimento ou armazenado no DB do Azure para servidor MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   ou
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome do host do servidor mestre
   - master_user: nome de usuário para o servidor mestre
   - master_password: a senha para o servidor mestre
   - master_log_file: nome de arquivo de log binário de `show master status` em execução
   - master_log_pos: posição de log binário de `show master status` em execução
   - master_gtid_pos: posição GTID de execução`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: Contexto do certificado de AC. Se você não estiver usando SSL, passe em uma seqüência vazia.*
    
    
    *Recomendamos passar no parâmetro master_ssl_ca como variável. Para obter mais informações, confira os exemplos a seguir.

   **Exemplos**

   - Replicação com SSL

       Crie a `@cert` variável executando os seguintes comandos:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       A replicação com O SSL é configurada entre um servidor mestre hospedado no companya.com de domínio e um servidor de réplica hospedado no Banco de Dados Do Azure para MariaDB. Esse procedimento armazenado é executado na réplica.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replicação sem SSL

       A replicação sem SSL é configurada entre um servidor mestre hospedado no companya.com de domínio e um servidor de réplica hospedado no Banco de Dados Do Azure para MariaDB. Esse procedimento armazenado é executado na réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Comece a replicação.

   Ligue `mysql.az_replication_start` para o procedimento armazenado para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Verifique o status da replicação.

   Ligue [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) para o comando no servidor de réplicas para visualizar o status de replicação.
    
   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` `Slave_SQL_Running` e estão `yes`no estado , `Seconds_Behind_Master` `0`e o valor de é , a replicação está funcionando. `Seconds_Behind_Master` indica o atraso da réplica. Se o valor `0`não for, então a réplica está processando atualizações.

4. Atualize as variáveis correspondentes do servidor para tornar a replicação de dados mais segura (necessária apenas para replicação sem GTID).
    
    Devido a uma limitação de replicação [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) nativa [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) no MariaDB, você deve definir e variáveis na replicação sem o cenário GTID.

    Verifique as variáveis `sync_master_info` e `sync_relay_log_info` as variáveis do servidor escravo para certificar-se de que `1`a replicação de dados está estável e defina as variáveis para .
    
## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para interromper a replicação entre o servidor primário e o de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Remova a relação de replicação

Para remover a relação entre o servidor mestre e réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Pular o erro de replicação

Para pular um erro de replicação e permitir a replicação, use o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Replicação de entrada de dados](concepts-data-in-replication.md) para o Banco de Dados do Azure para o MariaDB.
