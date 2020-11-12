---
title: Criar usuários-banco de dados do Azure para MariaDB
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um servidor de Banco de Dados do Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 882c8365bda87e97bfbc3bee9bdd320b312b4114
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542704"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Criar usuários no Banco de Dados do Azure para MariaDB 
Este artigo descreve como você pode criar usuários em um servidor de Banco de Dados do Azure para MariaDB.

> [!NOTE]
> Comunicação livre de desvio
>
> A Microsoft é compatível com um ambiente diversificado e inclusivo. Este artigo contém referências à palavra _escravo_. O [guia de estilo para comunicação sem desvios](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) da Microsoft reconhece esse termo como uma palavra excludente. A palavra é usada neste artigo para fins de consistência, uma vez que, atualmente, é a palavra que aparece no software. Quando o software for atualizado e esta palavra for removida, este artigo será atualizado para manter o alinhamento.
>

Quando foi criado o Banco de Dados do Azure para MariaDB, você forneceu um nome de usuário de logon de administrador de servidor e uma senha. Para saber mais, você pode seguir o [Início Rápido](quickstart-create-mariadb-server-database-using-azure-portal.md). Você pode localizar seu nome de usuário de logon de administrador do servidor do portal do Azure.

O usuário administrador de servidor obtém determinados privilégios para o servidor como listado: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Depois de criar o servidor de Banco de Dados do Azure para MariaDB, você poderá usar a primeira conta de usuário administrador de servidor para criar usuários adicionais e conceder acesso de administrador a eles. Além disso, a conta de administrador do servidor pode ser usada para criar usuários com menos privilégios que têm acesso aos esquemas de banco de dados individuais.

> [!NOTE]
> Não há suporte para a função SUPER Privilege e DBA. Examine os [privilégios](concepts-limits.md#privileges--data-manipulation-support) no artigo limitações para entender o que não tem suporte no serviço.<br><br>
> Os plug-ins de senha, como "validate_password" e "caching_sha2_password", não são suportados pelo serviço.

## <a name="create-additional-admin-users"></a>Criar usuários administradores adicionais
1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta de cliente preferida, como o MySQL Workbench, o mysql.exe, o HeidiSQL, entre outras. 
   Se você não tiver certeza de como se conectar, veja [Usar o MySQL Workbench para se conectar e consultar dados](./connect-workbench.md)

3. Edite e execute o código SQL a seguir. Substitua o seu novo nome de usuário para o valor de espaço reservado `new_master_user`. Esta sintaxe concede os privilégios listados em todos os esquemas de banco de dados ( *.* ) para o nome de usuário (new_master_user neste exemplo). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Verificar as concessões 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Criar usuários de banco de dados

1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta de cliente preferida, como o MySQL Workbench, o mysql.exe, o HeidiSQL, entre outras. 
   Se você não tiver certeza de como se conectar, veja [Usar o MySQL Workbench para se conectar e consultar dados](./connect-workbench.md)

3. Edite e execute o código SQL a seguir. Substitua o valor de espaço reservado `db_user` com seu novo nome de usuário pretendido e o valor de espaço reservado `testdb` com seu próprio nome de banco de dados.

   Essa sintaxe de código sql cria um novo banco de dados nomeado testdb para fins de exemplo. Em seguida, ele cria um novo usuário no serviço de Banco de Dados do Azure para MariaDB e concede a todos os privilégios para o novo esquema de banco de dados (testdb.\*) a esse usuário. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Verifique as concessões no banco de dados.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Faça logon no servidor, especificando o banco de dados designado, usando o novo nome de usuário e senha. Este exemplo mostra a linha de comando do mysql. Com este comando, será solicitada a senha para o nome de usuário. Substitua seu próprio nome de servidor, o nome do banco de dados e o nome de usuário.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Para obter mais informações sobre gerenciamento de contas de usuário, veja a documentação do MariaDB para [Gerenciamento de contas de usuário](https://mariadb.com/kb/en/library/user-account-management/), [Sintaxe de GRANT](https://mariadb.com/kb/en/library/grant/) e [Privilégios](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="azure_superuser"></a>azure_superuser

Todos os servidores do banco de dados do Azure para MySQL são criados com um usuário chamado "azure_superuser". Essa é uma conta de sistema criada pela Microsoft para gerenciar o servidor a fim de realizar monitoramento, backups e outras manutenções regulares. Os engenheiros de chamada também podem usar essa conta para acessar o servidor durante um incidente com autenticação de certificado e devem solicitar acesso usando processos JIT (just-in-time).

## <a name="next-steps"></a>Próximas etapas
Abra o firewall para os endereços IP das máquinas os novos usuários para que ele possa se conectar: [Criar e gerenciar regras de firewall para o Banco de Dados do Azure para MariaDB usando o portal do Azure](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
