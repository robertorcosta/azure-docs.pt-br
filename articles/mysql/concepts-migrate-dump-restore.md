---
title: Migrar usando despejo e restauração-banco de dados do Azure para MySQL
description: Este artigo descreve duas maneiras comuns de fazer backup e restaurar bancos de dados no seu Banco de dados do Azure para MySQL, usando ferramentas, como mysqldump, MySQL Workbench e PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: b15da2aa83231bfdc8732995888349b06ab56d15
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163770"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrar seu banco de dados MySQL para o Banco de Dados do Azure para MySQL usando despejo e restauração
Este artigo descreve duas maneiras comuns de fazer backup e restaurar bancos de dados no seu Banco de dados do Azure para MySQL
- Despejo e restauração da linha de comando (usando mysqldump) 
- Despejo e restauração usando o PHPMyAdmin 

## <a name="before-you-begin"></a>Antes de começar
Para acompanhar este guia de instruções, você precisa do seguinte:
- [Criar Banco de Dados do Azure para servidor MySQL - portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Utilitário da linha de comando [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalado em um computador.
- MySQL Workbench o [download do MySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para executar comandos de despejo e restauração.

## <a name="use-common-tools"></a>Usar ferramentas comuns
Use ferramentas e utilitários comuns, como MySQL Workbench ou mysqldump, para se conectar e restaurar dados remotamente para o Azure Database para MySQL. Use essas ferramentas no computador cliente com uma conexão de Internet para se conectar ao Banco de Dados do Azure para MySQL. Use uma conexão SSL criptografada para práticas recomendadas de segurança, consulte também [Configurar conectividade SSL no Banco de Dados do Azure para MySQL](concepts-ssl-connection-security.md). Você não precisa mover os seus arquivos de despejo para nenhum local específico na nuvem ao migrar para o Banco de Dados do Azure para MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Usos comuns de despejo e restauração
Você pode usar os utilitários de MySQL como mysqldump e mysqlpump para bancos de dados de despejo e de carga em um banco de dados MySQL em vários cenários comuns. Em outros cenários, você pode usar a abordagem de [Importação e exportação](concepts-migrate-import-export.md) em vez disso.

- Use despejos do banco de dados quando você estiver migrando um banco de dados inteiro. Essa recomendação é mantida ao mover uma grande quantidade de dados MySQL ou quando você quer minimizar a interrupção do serviço para aplicativos ou sites em tempo real. 
-  Verifique se todas as tabelas no banco de dados usam o mecanismo de armazenamento do InnoDB ao carregar dados no Banco de dados do Azure para MySQL. O Banco de dados do Azure para MySQL dá suporte apenas ao mecanismo de armazenamento do InnoDB e, portanto, não dá suporte a mecanismos de armazenamento alternativos. Se as tabelas foram configuradas com outros mecanismos de armazenamento, converta-as para o formato do mecanismo do InnoDB antes da migração para o Banco de dados do Azure para MySQL.
   Por exemplo, se você tiver um WordPress ou WebApp usando as tabelas MyISAM, primeiro converta essas tabelas migrando em formato de InnoDB antes de restaurar o banco de dados do Azure para MySQL. Use a cláusula `ENGINE=InnoDB` para definir o mecanismo usado ao criar uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes da restauração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidade, verifique se a mesma versão do MySQL é usada nos sistemas de origem e de destino ao despejar bancos de dados. Por exemplo, se seu servidor existente do MySQL tem a versão 5.7, então você deve migrar para o banco de dados do Azure para MySQL configurado para executar a versão 5.7. O comando `mysql_upgrade` não funciona em um banco de dados do Azure para o servidor MySQL e não é suportado. Se você precisar fazer upgrade das versões do MySQL, primeiro despeje ou exporte seu banco de dados da versão inferior para uma versão posterior do MySQL no seu ambiente. Em seguida, execute `mysql_upgrade`, antes de tentar migrar para um banco de dados do Azure para MySQL.

## <a name="performance-considerations"></a>Considerações sobre desempenho
Para otimizar o desempenho, observe essas considerações ao despejar grandes bancos de dados:
-   Use a opção `exclude-triggers` em mysqldump ao despejar bancos de dados. Exclua os gatilhos de arquivos de despejo para evitar que os comandos de gatilho acionem durante a restauração de dados. 
-   Use a opção `single-transaction` para definir o modo de isolamento da transação para REPEATABLE READ e enviar uma instrução SQL START TRANSACTION para o servidor antes de despejar os dados. Despejar muitas tabelas em uma única transação pode fazer com que alguns armazenamentos adicionais sejam consumidos durante a restauração. A opção `single-transaction` e a opção `lock-tables` são mutuamente exclusivas, porque LOCK TABLES faz com que as transações pendentes sejam confirmadas implicitamente. Para despejar tabelas grandes, combine a opção `single-transaction` com a opção `quick`. 
-   Use a sintaxe de várias linhas `extended-insert`, que inclui várias listas VALUE. O resultado será um arquivo de despejo menor e inserções mais rápidas quando o arquivo é recarregado.
-  Use a opção `order-by-primary` em mysqldump ao despejar bancos de dados, para que os dados sejam executados na ordem de chave primária.
-   Use a opção `disable-keys` em mysqldump ao despejar dados, para desabilitar as restrições de chave estrangeira antes da carga. Desabilitar as verificações de chave estrangeira proporciona ganhos de desempenho. Habilite as restrições e verifique os dados após o carregamento para garantir a integridade referencial.
-   Use tabelas particionadas, quando apropriado.
-   Carregar dados paralelamente. Evite o excesso de paralelismo que poderá fazer com que você atinja um limite de recursos e monitore os recursos usando as métricas disponíveis no portal do Azure. 
-   Use a opção `defer-table-indexes` em mysqlpump ao despejar bancos de dados, para que a criação de índice ocorra após os dados de tabelas que são carregados.
-   Use a opção `skip-definer` no mysqlpump para omitir as cláusulas refinadas e de segurança do SQL das instruções CREATE para exibições e procedimentos armazenados.  Quando você recarrega o arquivo de despejo, ele cria objetos que usam os valores de segurança padrão e SQL.
-   Copie os arquivos de backup para um blob/armazenamento do Azure e execute a restauração de lá, o que deve ser muito mais rápido do que executar a restauração pela Internet.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Criar um arquivo de backup a partir da linha de comando usando mysqldump
Para fazer backup de um banco de dados MySQL no servidor local ou em uma máquina virtual, execute o seguinte comando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os seguintes parâmetros devem ser fornecidos:
- [uname] Seu nome de usuário do banco de dados 
- [pass] A senha do banco de dados (observe que não há nenhum espaço entre -p e a senha) 
- [dbname] O nome do banco de dados 
- [backupfile.sql] O nome do arquivo para o backup do banco de dados 
- [-opt] A opção mysqldump 

Por exemplo, para fazer backup de um banco de dados chamado “testdb” no servidor MySQL com o nome de usuário “testuser” e nenhuma senha para um arquivo testdb_backup.sql, use o comando a seguir. O comando faz backup do banco de dados `testdb` em um arquivo chamado `testdb_backup.sql`, que contém todas as instruções SQL necessárias para recriar o banco de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas em seu banco de dados para backup, liste os nomes de tabela, separados por espaços. Por exemplo, para fazer backup somente das tabelas table1 e table2 de “testdb”, siga este exemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Para fazer backup de mais de um banco de dados ao mesmo tempo, use a opção --database e liste os nomes dos banco de dados separados por espaços. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Criar um banco de dados no Banco de dados de destino do Azure para o serviço MySQL
Crie um banco de dados vazio no Banco de dados de destino do Azure para servidor MySQL para o qual você deseja migrar os dados. Use uma ferramenta como o MySQL Workbench para criar o banco de dados. O banco de dados pode ter o mesmo nome que o banco de dados que contém os dados de despejo ou você pode criar um banco de dados com um nome diferente.

Para se conectar, localize as informações de conexão na página **Visão Geral** do Banco de Dados do Azure para MySQL.

![Encontrar as informações de conexão no portal do Azure](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Adicione as informações de conexão ao MySQL Workbench.

![Cadeia de Conexão do MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurar o banco de dados MySQL usando a linha de comando ou o MySQL Workbench
Depois de criar o banco de dados de destino, você pode usar o comando mysql ou o MySQL Workbench para restaurar os dados no banco de dados recém-criado do arquivo de despejo.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaure os dados no banco de dados recém-criado no banco de dados de destino para do Azure para o servidor MySQL.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportar usando PHPMyAdmin
Para exportar, você pode usar a ferramenta phpMyAdmin que você pode já ter instalado localmente em seu ambiente. Para exportar o banco de dados MySQL usando PHPMyAdmin:
1. Abra o phpMyAdmin.
2. Selecione o banco de dados. Clique no nome do banco de dados na lista à esquerda. 
3. Clique no link **Exportar**. Aparece uma nova página para exibir o despejo do banco de dados.
4. Na área de Exportação, clique no link **Selecionar Tudo** para selecionar as tabelas no banco de dados. 
5. Na área de opções do SQL, clique nas opções apropriadas. 
6. Clique na opção **Salvar como arquivo** e na opção de compactação correspondente e, em seguida, clique no botão **Ir**. Uma caixa de diálogo deve aparecer solicitando que você salve o arquivo localmente.

## <a name="import-using-phpmyadmin"></a>Importar usando PHPMyAdmin
Importar o banco de dados é semelhante à exportação. As seguintes ações ocorrem:
1. Abra o phpMyAdmin. 
2. Na página de configuração do phpMyAdmin, clique em **Adicionar** para adicionar o Banco de dados do Azure para o servidor MySQL. Forneça os detalhes de conexão e informações de logon.
3. Crie um banco de dados com o nome adequado e selecione-o na lista à esquerda da tela. Para reconfigurar o banco de dados existente, clique no nome do banco de dados, selecione todas as caixas de seleção ao lado dos nomes da tabela e selecione **Remover** para excluir as tabelas existentes. 
4. Clique no link **SQL** para mostrar a página onde você pode digitar os comandos SQL ou fazer upload do arquivo SQL. 
5. Use o botão **procurar** para localizar o arquivo do banco de dados. 
6. Clique no botão **Ir** para exportar o backup, execute os comandos SQL e recrie o banco de dados.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
- [Conectar aplicativos ao Banco de Dados do Azure para MySQL](./howto-connection-string.md).
- Para obter mais informações de como migrar bancos de dados para o Banco de Dados do Azure para MySQL, confira o [Guia de Migração de Banco de Dados](https://aka.ms/datamigration).
