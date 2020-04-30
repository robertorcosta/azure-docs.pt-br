---
title: Autorizar o acesso ao servidor e ao banco de dados usando contas de usuário e logons
description: Saiba mais sobre como o banco de dados SQL do Azure e o Azure Synapse Analytics autenticam usuários para acesso usando logons e contas de usuário. Saiba também como funções de banco de dados e permissões explícitas para autorizar logons e usuários a executar ações e consultar dados.
keywords: segurança do banco de dados SQL, gerenciamento de segurança de banco de dados, segurança de logon, segurança de banco de dados, acesso ao banco de dados
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/23/2020
ms.openlocfilehash: 0f1611e6d3524cc78fc20fed9d1aac6f3fd453fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106433"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Autorizando o acesso ao banco de dados a usuários autenticados no banco de dados SQL e no Azure Synapse Analytics usando logons e contas de usuário

Neste artigo, você aprenderá sobre:

- Opções para configurar o banco de dados SQL do Azure e a análise de Synapse do Azure (anteriormente Azure SQL Data Warehouse) para permitir que os usuários executem tarefas administrativas e acessem os dados armazenados nesses bancos.
- A configuração de acesso e autorização depois de criar inicialmente um novo banco de dados SQL do Azure
- Como adicionar logons e contas de usuário no banco de dados mestre e contas de usuário e conceder a essas contas permissões administrativas
- Como adicionar contas de usuário em bancos de dados de usuário, associadas a logons ou a contas de usuário independentes
- Configurar contas de usuário com permissões em bancos de dados de usuário usando funções de banco de dados e permissões explícitas

> [!IMPORTANT]
> No banco de dados SQL do Azure e no Azure Synapse Analytics (anteriormente, o Azure SQL Data Warehouse) são mencionados coletivamente no restante deste artigo, como bancos de dados ou como SQL do Azure (para simplificar).

## <a name="authentication-and-authorization"></a>Autenticação e autorização

A [**autenticação**](sql-database-security-overview.md#authentication) é o processo de provar que o usuário é quem alega ser. Um usuário se conecta a um banco de dados usando uma conta de usuário.
Quando um usuário tenta se conectar a um banco de dados, ele fornece uma conta de usuário e informações de autenticação. O usuário é autenticado usando um dos dois métodos de autenticação a seguir:

- [Autenticação do SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Com esse método de autenticação, o usuário envia um nome de conta de usuário e uma senha associada para estabelecer uma conexão. Essa senha é armazenada no banco de dados mestre para contas de usuário vinculadas a um logon ou armazenadas no banco de dados que contém as contas de usuário *não* vinculadas a um logon.
- [Autenticação do Azure Active Directory](sql-database-aad-authentication.md)

  Com esse método de autenticação, o usuário envia um nome de conta de usuário e solicita que o serviço use as informações de credenciais armazenadas no Azure Active Directory.

**Logons e usuários**: no Azure SQL, uma conta de usuário em um banco de dados pode ser associada a um logon que é armazenado no banco de dados mestre ou pode ser um nome de usuário que é armazenado em um banco de dados individual.

- Um **logon** é uma conta individual no banco de dados mestre, para a qual uma conta de usuário em um ou mais bancos podem ser vinculadas. Com um logon, as informações de credencial da conta de usuário são armazenadas com o logon.
- Uma **conta de usuário** é uma conta individual em qualquer banco de dados que pode ser, mas não precisa estar vinculada a um logon. Com uma conta de usuário que não está vinculada a um logon, as informações de credenciais são armazenadas com a conta de usuário.

A [**autorização**](sql-database-security-overview.md#authorization) para acessar dados e executar várias ações é gerenciada usando funções de banco de dado e permissões explícitas. A autorização refere-se às permissões atribuídas a um usuário e determina o que o usuário tem permissão para fazer. A autorização é controlada pelas [associações de função](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) de banco de dados da sua conta de usuário e [permissões de nível de objeto](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Logons existentes e contas de usuário depois de criar um novo banco de dados

Ao criar sua primeira implantação do Azure SQL, você especifica um logon de administrador e uma senha associada para esse logon. Essa conta administrativa é chamada de **administrador do servidor**. A seguinte configuração de logons e usuários nos bancos de dados mestre e de usuário ocorre durante a implantação:

- Um logon do SQL com privilégios administrativos é criado usando o nome de logon especificado. Um [logon](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) é uma conta de usuário individual para fazer logon no banco de dados SQL.
- Esse logon recebe permissões administrativas completas em todos os bancos de dados como uma [entidade de segurança no nível do servidor](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Esse logon tem todas as permissões disponíveis no banco de dados SQL e não pode ser limitado. Em uma instância gerenciada, esse logon é adicionado à [função de servidor fixa sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (essa função não existe com bancos de dados únicos ou em pool).
- Uma [conta](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) de usuário `dbo` chamada é criada para esse logon em cada banco de dados de usuário. O usuário [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) tem todas as permissões de banco de dados no banco de dados `db_owner` e é mapeado para a função de banco de dados fixa. Funções de banco de dados fixas adicionais são discutidas posteriormente neste artigo.

Para identificar as contas de administrador de um banco de dados, abra o portal do Azure e navegue até a guia **Propriedades** do servidor ou instância gerenciada.

![Administradores do SQL Server](media/sql-database-manage-logins/sql-admins.png)

![Administradores do SQL Server](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> O nome de logon do administrador não pode ser alterado após ter sido criado. Para redefinir a senha para o administrador do servidor lógico, vá para o [portal do Azure](https://portal.azure.com), clique em **servidores SQL**, selecione o servidor na lista e clique em **Redefinir senha**. Para redefinir a senha de um servidor de instância gerenciada, vá para o portal do Azure, clique na instância e clique em **Redefinir senha**. Você também pode usar o PowerShell ou o CLI do Azure.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Criar logons e usuários adicionais com permissões administrativas

Neste ponto, sua instância do SQL do Azure só é configurada para acesso usando uma única conta de usuário e logon do SQL. Para criar logons adicionais com permissões administrativas totais ou parciais, você tem as seguintes opções (dependendo do seu modo de implantação):

- **Criar uma conta de administrador de Azure Active Directory com permissões administrativas completas**

  Habilite a autenticação Azure Active Directory e crie um logon de administrador do Azure AD. Uma conta de Azure Active Directory pode ser configurada como um administrador da implantação do banco de dados SQL com permissões administrativas completas. Essa conta pode ser uma conta de grupo individual ou de segurança. Um administrador do Azure AD **deve** ser configurado se você quiser usar contas do Azure ad para se conectar ao banco de dados SQL. Para obter informações detalhadas sobre como habilitar a autenticação do Azure AD para todos os tipos de implantação do banco de dados SQL, consulte os seguintes artigos:

  - [Use a Autenticação do Azure Active Directory para autenticar com SQL](sql-database-aad-authentication.md)
  - [Configurar e gerenciar autenticação do Azure Active Directory com SQL](sql-database-aad-authentication-configure.md)

- **Em uma implantação de instância gerenciada, crie logons SQL com permissões administrativas completas**

  - Criar um logon de SQL Server adicional na instância gerenciada
  - Adicione o logon à [função de servidor fixa sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) usando a instrução [ALTER Server Role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) . Esse logon terá permissões administrativas completas.
  - Como alternativa, crie um [logon do Azure ad](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) usando a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login</a> .

- **Em uma implantação única ou em pool, crie logons do SQL com permissões administrativas limitadas**

  - Criar um logon do SQL adicional no banco de dados mestre para uma implantação de banco de dados individual ou em pool, ou uma implantação de instância gerenciada
  - Criar uma conta de usuário no banco de dados mestre associado a este novo logon
  - Adicione a conta de usuário ao `dbmanager`, à `loginmanager` função ou ambas no banco de `master` dados usando a instrução [ALTER Server Role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (para o Azure Synapse Analytics, use a instrução [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > `dbmanager`e `loginmanager` as funções **não** pertencem a implantações de instância gerenciada.

  Os membros dessas [funções de banco de dados mestre especiais](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) para bancos de dados individuais ou em pool permitem que os usuários tenham autoridade para criar e gerenciar bancos de dados ou para criar e gerenciar logons. Em bancos de dados criados por um usuário que seja membro da `dbmanager` função, o membro é mapeado para a função de `db_owner` banco de dados fixa e pode fazer logon e gerenciar esse banco de `dbo` dados usando a conta de usuário. Essas funções não têm permissões explícitas fora do banco de dados mestre.

  > [!IMPORTANT]
  > Você não pode criar um logon do SQL adicional com permissões administrativas completas em um banco de dados único ou em pool.

## <a name="create-accounts-for-non-administrator-users"></a>Criar contas para usuários não administradores

Você pode criar contas para usuários não administrativos usando um dos dois métodos:

- **Criar um logon**

  Crie um logon SQL no banco de dados mestre. Em seguida, crie uma conta de usuário em cada banco de dados ao qual o usuário precisa de acesso e associe a conta de usuário a esse logon. Essa abordagem é preferida quando o usuário deve acessar vários bancos de dados e você deseja manter as senhas sincronizadas. No entanto, essa abordagem tem complexidades quando usadas com replicação geográfica, já que o logon deve ser criado no servidor primário e nos servidores secundários. Para obter mais informações, consulte [configurar e gerenciar a segurança do banco de dados SQL do Azure para restauração geográfica ou failover](sql-database-geo-replication-security-config.md).
- **Criar uma conta do usuário**

  Crie uma conta de usuário no banco de dados para a qual um usuário precisa de acesso (também chamado de [usuário independente](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Com um banco de dados único ou em pool, você sempre pode criar esse tipo de conta de usuário.
  - Com um banco de dados de instância gerenciada que não dá suporte a [entidades de servidor do Azure ad](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), você só pode criar esse tipo de conta de usuário em um [banco de dados independente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Com a instância gerenciada que dá suporte a [entidades de servidor do Azure ad](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), você pode criar contas de usuário para autenticar a instância gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados independente.

  Com essa abordagem, as informações de autenticação de usuário são armazenadas em cada banco de dados e replicadas automaticamente para bancos de dados replicados geograficamente. No entanto, se a mesma conta existir em vários bancos de dados e você estiver usando a autenticação do SQL, você deverá manter as senhas sincronizadas manualmente. Além disso, se um usuário tiver uma conta em bancos de dados diferentes com senhas diferentes, lembrar-se dessas senhas poderá se tornar um problema.

> [!IMPORTANT]
> Para criar usuários independentes mapeados para identidades do Azure AD, você deve estar conectado usando uma conta do Azure AD que seja um administrador no banco de dados SQL. Na instância gerenciada, um logon do `sysadmin` SQL com permissões também pode criar um logon ou usuário do Azure AD.

Para obter exemplos que mostram como criar logons e usuários, consulte:

- [Criar logon para bancos de dados individuais ou em pool](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Criar logon para banco de dados de instância gerenciada](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Criar logon para o banco de dados do Azure Synapse Analytics](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Criar usuário](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Criando usuários continhantes do Azure AD](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Para um tutorial de segurança que inclui a criação de SQL Server um usuário independente em um banco de dados único ou em pool, consulte [tutorial: proteger um banco de dados individual ou em pool](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Usando funções de banco de dados fixas e personalizadas

Depois de criar uma conta de usuário em um banco de dados, seja com base em um logon ou como um usuário independente, você pode autorizar esse usuário a executar várias ações e acessar dados em um banco de dado específico. Você pode usar os seguintes métodos para autorizar o acesso:

- **Funções de banco de dados fixas**

  Adicione a conta de usuário a uma [função de banco de dados fixa](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Há 9 funções de banco de dados fixas, cada uma com um conjunto definido de permissões. As funções de banco de dados fixas mais comuns são: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**e **db_denydatareader**. **db_owner** é usada normalmente para conceder permissão total a apenas alguns usuários. As outras funções fixas de banco de dados são úteis para mover rapidamente um banco de dados simples para desenvolvimento, mas não são recomendadas para a maioria dos bancos de dados de produção. Por exemplo, a função de banco de dados fixa **db_datareader** concede acesso de leitura a todas as tabelas no banco de dados, o que é estritamente necessário.

  - Para adicionar um usuário a uma função de banco de dados fixa:

    - No banco de dados SQL do Azure, use a instrução [ALTER role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) . Para obter exemplos, consulte [ALTER role examples](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse Analytics, use a instrução [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) . Para obter exemplos, consulte [sp_addrolemember exemplos](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Função de banco de dados personalizada**

  Crie uma função de banco de dados personalizada usando a instrução [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) . Uma função personalizada permite que você crie suas próprias funções de banco de dados definidas pelo usuário e conceda cuidadosamente a cada função as permissões mínimas necessárias para a necessidade comercial. Em seguida, você pode adicionar usuários à função personalizada. Quando um usuário for membro de várias funções, ele agregará as permissões de todas elas.
- **Conceder permissões diretamente**

  Conceda as [permissões](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) de conta de usuário diretamente. Há mais de 100 permissões que podem ser concedidas ou negadas individualmente no Banco de Dados SQL. Muitas dessas permissões são aninhadas. Por exemplo, a permissão `UPDATE` em um esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Assim como ocorre na maioria dos sistemas de permissão, a negação de uma permissão substitui uma concessão. Devido à natureza aninhada e ao número de permissões, talvez seja necessário realizar um estudo cuidadoso para criar um sistema de permissões apropriado a fim de proteger corretamente o banco de dados. Comece com a lista de permissões em [Permissões (Mecanismo do Banco de Dados)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) e examine o [gráfico com tamanho de pôster](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) das permissões.

## <a name="using-groups"></a>Using groups

O gerenciamento de acesso eficiente usa permissões atribuídas a Active Directory grupos de segurança e funções fixas ou personalizadas em vez de usuários individuais.

- Ao usar a autenticação do Azure Active Directory, coloque Azure Active Directory usuários em um grupo de segurança do Azure Active Directory. Crie um usuário de banco de dados independente para o grupo. Coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas para esse grupo de usuários.

- Ao usar a autenticação do SQL, crie usuários de banco de dados independente no banco de dados. Coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas para esse grupo de usuários.

  > [!NOTE]
  > Você também pode usar grupos para usuários de banco de dados não independentes.

Você deve estar familiarizado com os seguintes recursos que podem ser usados para limitar ou elevar as permissões:

- [Representação](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e [assinatura de módulo](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) podem ser usadas para elevar permissões temporariamente com segurança.
- [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pode ser usado para limitar quais linhas de um usuário pode acessar.
- [Mascaramento de dados](sql-database-dynamic-data-masking-get-started.md) pode ser usado para limitar a exposição de dados confidenciais.
- [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral de todos os recursos de segurança do Banco de Dados SQL, veja a [visão geral de segurança do SQL](sql-database-security-overview.md).
