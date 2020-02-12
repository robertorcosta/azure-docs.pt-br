---
title: Proteger um banco de dados
description: Dicas para proteger um banco de dados e desenvolver soluções no recurso de pool de SQL do SQL Analytics.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26cdbb1fc2899d1b03fea6199074467623706c63
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153274"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger um banco de dados no SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Este artigo explicará as noções básicas de como proteger seu pool SQL na análise de SQL. Em particular, este artigo apresenta os recursos para limitar o acesso, proteger dados e monitorar atividades em um banco de dados provisionado usando o pool do SQL.

## <a name="connection-security"></a>Segurança da conexão
A Segurança da Conexão refere-se a como você restringe e protege as conexões com o banco de dados usando regras de firewall e criptografia de conexão.

As regras de firewall são usadas pelo servidor e pelo banco de dados para rejeitar tentativas de conexão de endereços IP que não foram explicitamente na lista de permissões. Para permitir conexões do endereço IP público do seu aplicativo ou computador cliente, você deve primeiro criar uma regra de firewall no nível de servidor usando o portal do Azure, a API REST ou o PowerShell. 

Como prática recomendada, você deve restringir ao máximo os intervalos de endereços IP permitidos por meio do firewall de servidor.  Para acessar o pool do SQL do seu computador local, verifique se o firewall na rede e no computador local permite a comunicação de saída na porta TCP 1433.  

O Azure Synapse Analytics usa regras de firewall de IP no nível de servidor. Ele não dá suporte a regras de firewall de IP no nível de banco de dados. Para obter mais informações, consulte [regras de firewall do banco de dados SQL do Azure](../sql-database/sql-database-firewall-configure.md)

As conexões com o pool SQL são criptografadas por padrão.  A modificação das configurações de conexão para desabilitar a criptografia é ignorada.

## <a name="authentication"></a>Autenticação
A Autenticação refere-se a como você comprova sua identidade durante a conexão com o banco de dados. Atualmente, o pool do SQL dá suporte à autenticação SQL Server com um nome de usuário e uma senha e com Azure Active Directory. 

Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo", por meio da Autenticação do SQL Server.

No entanto, como uma melhor prática, os usuários de sua organização devem usar uma conta diferente para a autenticação. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas, caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL. 

Para criar um usuário Autenticado do SQL Server, conecte o banco de dados **mestre** no servidor com o logon de administrador do servidor e crie um novo logon do servidor.  É uma boa ideia também criar um usuário no banco de dados mestre do Azure Synapse usuários. A criação de um usuário mestre permite que um usuário faça logon usando ferramentas, como o SSMS, sem especificar um nome de banco de dados.  Ela também permite que o usuário utilize o pesquisador de objetos para exibir todos os bancos de dados em um SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, conecte-se ao **banco de dados do pool SQL** com o logon de administrador do servidor e crie um usuário de banco de dados com base no logon do servidor que você criou.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Para conceder um permissão de usuário para realizar operações adicionais, como criar logons ou novos bancos de dados, atribua o usuário às funções `Loginmanager` e `dbmanager` no banco de dados mestre. 

Para obter mais informações sobre essas funções adicionais e a autenticação em um banco de dados SQL, consulte [Managing bancos de dados e logons in Azure SQL Database](../sql-database/sql-database-manage-logins.md).  Para obter mais informações sobre como se conectar usando Azure Active Directory, consulte [conectando usando a autenticação do Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorização
A autorização refere-se ao que você pode fazer em um banco de dados quando você está autenticado e conectado. Privilégios de autorização são determinados pelas permissões e associações de função. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. Para gerenciar funções, você pode usar os seguintes procedimentos armazenados:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com a qual você está se conectando é um membro de db_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo.

Há maneiras de limitar ainda mais o que um usuário pode fazer em um data warehouse:

* [As permissões](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) granulares permitem controlar quais operações você pode fazer em colunas individuais, tabelas, exibições, esquemas, procedimentos e outros objetos no banco de dados. Use permissões granulares para ter maior controle e conceder as permissões mínimas necessárias. 
* [Funções de banco de dados](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15) diferentes de db_datareader e db_datawriter podem ser usadas para criar contas de usuário de aplicativo mais potentes ou contas de gerenciamento menos potentes. As funções internas de banco de dados fixo fornecem uma maneira fácil para conceder permissões, mas podem resultar na concessão de mais permissões do que o necessário.
* [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

O exemplo a seguir concede acesso de leitura a um esquema definido pelo usuário.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

O gerenciamento de bancos de dados e de servidores lógicos pelo portal do Azure ou usando a API do Azure Resource Manager é controlado pelas atribuições de função da sua conta de usuário. Para obter mais informações, consulte [controle de acesso baseado em função no portal do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure).

## <a name="encryption"></a>Criptografia
A Transparent Data Encryption (TDE) ajuda a proteger contra a ameaça de atividades mal-intencionadas Criptografando e descriptografando seus dados em repouso. Quando você criptografa seus banco de dados, os arquivos de log de transações e backups associados são criptografados sem exigir nenhuma alteração em seus aplicativos. A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. 

No Banco de Dados SQL, a chave de criptografia do banco de dados é protegida por um certificado do servidor interno. O certificado de servidor interno é exclusivo para cada servidor do Banco de Dados SQL. A Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. O algoritmo de criptografia usado é o AES-256. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15).

Você pode criptografar seu banco de dados usando o [portal do Azure](sql-data-warehouse-encryption-tde.md) ou o [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Próximas etapas
Para obter detalhes e exemplos sobre como se conectar ao seu warehouse com protocolos diferentes, consulte [conectar-se ao pool do SQL](sql-data-warehouse-connect-overview.md).
