---
title: Resolver corrupção de banco de dados-banco de dados do Azure para MySQL
description: Saiba mais sobre como corrigir problemas de corrupção de banco de dados para o banco de dados do Azure para MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933170"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Solucionar problemas de corrupção do banco de dados no Azure para MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Corrupção de banco de dados pode causar tempo de inatividade para seu aplicativo e também é essencial para resolver o problema no tempo para evitar a perda de dados. Quando ocorrer corrupção do banco de dados, você verá em seu servidor registra esse erro **InnoDB: a página do banco de dados está corrompida em disco ou falha**.

Neste guia, você aprenderá a corrigir se o banco de dados ou a tabela está corrompida. O banco de dados do Azure para MySQL usa o mecanismo InnoDB e oferece operações de reparo e verificação de corrupção automatizadas. O InnoDB verifica as páginas corrompidas executando somas de verificação em todas as páginas que lê e, se encontrar uma discrepância de soma de verificação, ele interromperá automaticamente o servidor MySQL.

Tente qualquer uma dessas opções abaixo para ajudar a reduzir rapidamente os problemas de corrupção do banco de dados.

## <a name="restart-your-mysql-server"></a>Reiniciar o servidor MySQL

Normalmente, você percebe que um banco de dados ou tabela está corrompido quando seu aplicativo acessa esse banco de dados da tabela ro. Como o InnoDB apresenta um mecanismo de recuperação de falhas que pode resolver a maioria dos problemas quando o servidor é reiniciado. Portanto, reiniciar o servidor deve ajudar o servidor a se recuperar de uma falha que fez com que o banco de dados esteja em estado inadequado.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>Resolver dados corrompidos com o método dump e Restore

É recomendável resolver o problema de corrupção com um **método de despejo e restauração**. Isso envolve a obtenção de acesso à tabela corrompida, usando o utilitário **mysqldump** para criar um backup lógico da tabela, que manterá a estrutura da tabela e os dados dentro dela e, em seguida, recarregando a tabela de volta para o banco de dados.

### <a name="backup-your-database-or-tables"></a>Fazer backup de seu banco de dados ou tabelas

> [!Important]
> - Faça com que o tenha configurado uma regra de firewall para acessar o servidor do computador cliente. Consulte como configurar a [regra de firewall em servidor único](howto-manage-firewall-using-portal.md) e [regra de firewall no servidor flexível](flexible-server/how-to-connect-tls-ssl.md).
> - Usar a opção SSL ```--ssl-cert``` para **mysqldump** se o SSL estiver habilitado

Criar um arquivo de backup da linha de comando usando mysqldump usando este comando

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os seguintes parâmetros devem ser fornecidos:
- [SSL-CERT =/Path/to/PEM] Baixe o certificado SSL em seu computador cliente e defina o caminho nele no comando. Não use esse SSL está desabilitado.
- [host] é o banco de dados do Azure para o servidor MySQL
- [uname] é o nome de usuário do administrador do servidor
- [pass] é a senha para o usuário administrador
- [dbname] é o nome do seu banco de dados
- [backupfile. SQL] se o nome do arquivo para o backup do banco de dados

> [!Important]
> - Para servidor único, use o formato ```admin-user@servername``` para substituir ```myserveradmin``` os comandos abaixo.
> - Para servidor flexível, use o formato ```admin-user``` para substituir ```myserveradmin``` os comandos abaixo.

Se uma tabela específica estiver corrompida, selecione tabelas específicas no banco de dados para fazer backup usando este exemplo
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Para fazer backup de um ou mais bancos de dados, use a opção--Database e liste os nomes de banco de dados separados por espaços.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>Restaurar seu banco de dados ou tabelas

As etapas a seguir mostram como o TP restaura seu banco de dados ou tabelas. Depois que o arquivo de backup for criado, você poderá restaurar a tabela ou os bancos de dados usando o utilitário**MySQL** . Execute o comando, conforme mostrado abaixo:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Aqui está um exemplo da restauração ```testdb``` do arquivo de backup criado com **mysqldump**. 

> [!Important]
> - Para servidor único, use o formato ```admin-user@servername``` para substituir ```myserveradmin``` no comando a seguir.
> - Para servidor flexível, use o formato ```admin-user``` para substituir ```myserveradmin``` no comando abaixo. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Próximas etapas
Se as etapas acima não ajudarem a resolver o problema, você sempre poderá restaurar todo o servidor.
- [Restaurar um servidor único do banco de dados do Azure para MySQL](howto-restore-server-portal.md)
- [Restaurar um servidor flexível do banco de dados do Azure para MySQL](flexible-server/how-to-restore-server-portal.md)



