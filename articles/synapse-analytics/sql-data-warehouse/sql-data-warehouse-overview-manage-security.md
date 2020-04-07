---
title: Proteja um banco de dados
description: Dicas para garantir um banco de dados e desenvolver soluções em um recurso de pool Synapse SQL.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 0c30294f2ca139a602074a980810e7c6737c4e2d
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742995"
---
# <a name="secure-a-database-in-azure-synapse"></a>Proteja um banco de dados no Azure Synapse

> [!div class="op_single_selector"]
>
> * [Visão geral da segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Este artigo irá levá-lo através do básico de garantir o seu pool Synapse SQL. Em particular, este artigo faz com que você comece com recursos para limitar o acesso, proteger dados e monitorar atividades em um banco de dados provisionado usando pool SQL.

## <a name="connection-security"></a>Segurança da conexão

A Segurança da Conexão refere-se a como você restringe e protege as conexões com o banco de dados usando regras de firewall e criptografia de conexão.

As regras de firewall são usadas tanto pelo servidor quanto pelo banco de dados para rejeitar tentativas de conexão de endereços IP que não tenham sido explicitamente listados na lista branca. Para permitir conexões do endereço IP público do seu aplicativo ou computador cliente, você deve primeiro criar uma regra de firewall no nível de servidor usando o portal do Azure, a API REST ou o PowerShell.

Como prática recomendada, você deve restringir ao máximo os intervalos de endereços IP permitidos por meio do firewall de servidor.  Para acessar o pool SQL do seu computador local, certifique-se de que o firewall em sua rede e computador local permita a comunicação de saída na porta TCP 1433.  

O Azure Synapse Analytics usa regras de firewall IP em nível de servidor. Ele não suporta regras de firewall IP em nível de banco de dados. Para obter mais informações, consulte as [regras de firewall do Banco de Dados SQL do Azure](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

As conexões ao seu pool SQL são criptografadas por padrão.  A modificação das configurações de conexão para desabilitar a criptografia é ignorada.

## <a name="authentication"></a>Autenticação

A Autenticação refere-se a como você comprova sua identidade durante a conexão com o banco de dados. O pool SQL atualmente suporta autenticação do servidor SQL com um nome de usuário e senha, e com o Azure Active Directory.

Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo", por meio da Autenticação do SQL Server.

No entanto, como uma prática recomendada, os usuários da sua organização devem usar uma conta diferente para autenticar. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas, caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL.

Para criar um usuário Autenticado do SQL Server, conecte o banco de dados **mestre** no servidor com o logon de administrador do servidor e crie um novo logon do servidor.  É uma boa ideia também criar um usuário no banco de dados principal. A criação de um usuário mestre permite que um usuário faça logon usando ferramentas, como o SSMS, sem especificar um nome de banco de dados.  Ela também permite que o usuário utilize o pesquisador de objetos para exibir todos os bancos de dados em um SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, conecte-se ao banco de **dados do pool SQL** com o login do administrador do servidor e crie um usuário de banco de dados com base no login do servidor que você criou.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Para conceder um permissão de usuário para realizar operações adicionais, como criar logons ou novos bancos de dados, atribua o usuário às funções `Loginmanager` e `dbmanager` no banco de dados mestre.

Para obter mais informações sobre essas funções adicionais e como fazer a autenticação em um Banco de Dados SQL, confira [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](../../sql-database/sql-database-manage-logins.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Para obter mais informações sobre como conectar usando o Azure Active Directory, consulte [Conectando usando a autenticação do diretório ativo do Azure](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorização

A autorização refere-se ao que você pode fazer dentro de um banco de dados uma vez que você está autenticado e conectado. Privilégios de autorização são determinados pelas permissões e associações de função. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. Para gerenciar funções, você pode usar os seguintes procedimentos armazenados:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com a qual você está se conectando é um membro de db_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo.

Existem maneiras de limitar ainda mais o que um usuário pode fazer dentro do banco de dados:

* [Permissões](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) granulares permitem controlar quais operações você pode fazer em colunas, tabelas, exibições, esquemas, procedimentos e outros objetos individuais no banco de dados. Use permissões granulares para ter maior controle e conceder as permissões mínimas necessárias.
* [Funções de banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) que não db_datareader e db_datawriter podem ser usadas para criar contas de usuários de aplicativos mais poderosas ou contas de gerenciamento menos poderosas. As funções internas de banco de dados fixo fornecem uma maneira fácil para conceder permissões, mas podem resultar na concessão de mais permissões do que o necessário.
* [Procedimentos armazenados](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

O exemplo a seguir concede acesso de leitura a um esquema definido pelo usuário.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

O gerenciamento de bancos de dados e de servidores lógicos pelo portal do Azure ou usando a API do Azure Resource Manager é controlado pelas atribuições de função da sua conta de usuário. Para saber mais, confira [Role-based access control in the Azure portal](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)(Controle de acesso baseado em função no portal do Azure).

## <a name="encryption"></a>Criptografia

O TDE (Transparent Data Encryption, criptografia de dados transparente) ajuda a proteger contra a ameaça de atividade maliciosa, criptografando e descriptografando seus dados em repouso. Quando você criptografa seus banco de dados, os arquivos de log de transações e backups associados são criptografados sem exigir nenhuma alteração em seus aplicativos. A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados.

No Banco de Dados SQL, a chave de criptografia do banco de dados é protegida por um certificado do servidor interno. O certificado de servidor interno é exclusivo para cada servidor do Banco de Dados SQL. A Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. O algoritmo de criptografia usado é a AES-256. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Você pode criptografar o banco de dados usando o [portal do Azure](sql-data-warehouse-encryption-tde.md) ou o [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes e exemplos sobre como se conectar ao seu armazém com diferentes protocolos, consulte [Conectar-se ao pool SQL](sql-data-warehouse-connect-overview.md).
