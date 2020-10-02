---
title: Criar bancos de dados e usuários-Azure para MySQL
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um servidor de Banco de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: ed653ffb6fc24a75170d51d345c0c64724ff90f1
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651014"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql-server"></a>Criar bancos de dados e usuários no Azure Database para MySQL Server

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Este artigo descreve como você pode criar usuários em um servidor de Banco de Dados do Azure para MySQL.

> [!NOTE]
> Comunicação livre de desvio
>
> A Microsoft dá suporte a um ambiente diversificado e de inclusão. Este artigo contém referências à palavra _subordinada_. O [Guia de estilo da Microsoft para comunicação sem tendência](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isso como uma palavra de exclusão. A palavra é usada neste artigo para fins de consistência porque, atualmente, ela é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

Quando foi criado o Banco de Dados do Azure para MySQL, você forneceu um nome de usuário de logon de administrador de servidor e uma senha. Para saber mais, você pode seguir o [Início Rápido](quickstart-create-mysql-server-database-using-azure-portal.md). Você pode localizar seu nome de usuário de logon de administrador do servidor do portal do Azure.

O usuário administrador do servidor obtém determinados privilégios para o servidor, conforme listado: 

   SELECIONAR, INSERIR, ATUALIZAR, EXCLUIR, CRIAR, DESCARTAR, RECARREGAR, PROCESSAR, REFERENCIAR, INDEXAR, ALTERAR, MOSTRAR BANCOS DE DADOS, CRIAR TABELAS TEMPORÁRIAS, BLOQUEAR TABELAS, EXECUTAR, REPLICAÇÃO SUBORDINADA, CLIENTE DE REPLICAÇÃO, CRIAR EXIBIÇÃO, MOSTRAR EXIBIÇÃO, CRIAR ROTINA, ALTERAR ROTINA, CRIAR USUÁRIO, EVENTO, GATILHO


Depois de criar o servidor de Banco de Dados do Azure para MySQL, você poderá usar a primeira conta de usuário de administrador de servidor para criar usuários adicionais e conceder acesso de administrador a eles. Além disso, a conta de administrador do servidor pode ser usada para criar usuários com menos privilégios que têm acesso aos esquemas de banco de dados individuais.

> [!NOTE]
> Não há suporte para a função SUPER Privilege e DBA. Examine os [privilégios](concepts-limits.md#privileges--data-manipulation-support) no artigo limitações para entender o que não tem suporte no serviço.<br><br>
> Os plug-ins de senha, como "validate_password" e "caching_sha2_password", não são suportados pelo serviço.

## <a name="how-to-create-database-with-non-admin-user-in-azure-database-for-mysql"></a>Como criar um banco de dados com um usuário não administrador no banco de dados do Azure para MySQL

1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure.

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta de cliente preferida, como o MySQL Workbench, o mysql.exe, o HeidiSQL, entre outras.
   Se você não tiver certeza de como se conectar, consulte como usar o MySQL Workbench para [se conectar e consultar dados para um único servidor](./connect-workbench.md) ou [conectar e consultar dados para um servidor flexível](./flexible-server/connect-workbench.md)

3. Edite e execute o código SQL a seguir. Substitua o valor de espaço reservado `db_user` com seu novo nome de usuário pretendido e o valor de espaço reservado `testdb` com seu próprio nome de banco de dados.

   Essa sintaxe de código sql cria um novo banco de dados nomeado testdb para fins de exemplo. Em seguida, ele cria um novo usuário no serviço do MySQL e concede a todos os privilégios para o novo esquema de banco de dados (testdb.\*) para o usuário.

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

# <a name="single-server"></a>[Servidor único](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
# <a name="flexible-server"></a>[Servidor flexível](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Como criar usuários administradores adicionais no Banco de Dados do Azure para MySQL

1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure.

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta de cliente preferida, como o MySQL Workbench, o mysql.exe, o HeidiSQL, entre outras.
   Se você não tiver certeza de como se conectar, veja [Usar o MySQL Workbench para se conectar e consultar dados](./connect-workbench.md)

3. Edite e execute o código SQL a seguir. Substitua o seu novo nome de usuário para o valor de espaço reservado `new_master_user`. Esta sintaxe concede os privilégios listados em todos os esquemas de banco de dados (*.*) para o nome de usuário (new_master_user neste exemplo).

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

## <a name="azure_superuser"></a>azure_superuser

Todos os servidores do banco de dados do Azure para MySQL são criados com um usuário chamado "azure_superuser". Essa é uma conta de sistema criada pela Microsoft para gerenciar o servidor a fim de realizar monitoramento, backups e outras manutenções regulares. Os engenheiros de chamada também podem usar essa conta para acessar o servidor durante um incidente com autenticação de certificado e devem solicitar acesso usando processos JIT (just-in-time).

## <a name="next-steps"></a>Próximas etapas

Abra o firewall dos endereços IP dos computadores dos novos usuários para permitir que conectem-se:
- [Criar e gerenciar regras de firewall em um único servidor](howto-manage-firewall-using-portal.md) 
- [ Criar e gerenciar regras de firewall no servidor flexível](flexible-server/how-to-connect-tls-ssl.md)

Para saber mais sobre gerenciamento de contas de usuário, consulte a documentação do MySQL para [Gerenciamento de contas de usuário](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [Sintaxe de GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html) e [Privilégios](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
