---
title: Autorizar acesso a servidores e bancos de dados usando logins e contas de usuário
description: Saiba como o Azure SQL Database e o Azure Synapse Analytics autenticam os usuários para acesso usando logins e contas de usuário. Aprenda também como fazer funções de banco de dados e permissões explícitas para autorizar logins e usuários a realizar ações e consultar dados.
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
ms.openlocfilehash: 98c15fe11b64e8c177e60a2ea1eb7c50eaf69353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124795"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Autorizando o acesso do banco de dados a usuários autenticados ao SQL Database e ao Azure Synapse Analytics usando logins e contas de usuário

Neste artigo, você aprende sobre:

- Opções para configurar o Banco de Dados Azure SQL e o Azure Synapse Analytics (antigo Azure SQL Data Warehouse) para permitir que os usuários realizem tarefas administrativas e acessem os dados armazenados nesses bancos de dados.
- A configuração de acesso e autorização depois de criar inicialmente um novo Banco de Dados SQL do Azure
- Como adicionar logins e contas de usuário no banco de dados principal e contas de usuário e, em seguida, conceder a essas contas permissões administrativas
- Como adicionar contas de usuário em bancos de dados de usuários, associados a logins ou como contas de usuário contidas
- Configure contas de usuário com permissões em bancos de dados de usuários usando funções de banco de dados e permissões explícitas

> [!IMPORTANT]
> Os bancos de dados no Azure SQL Database e no Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse) são referidos coletivamente no restante deste artigo como bancos de dados ou como Azure SQL (para simplicidade).

## <a name="authentication-and-authorization"></a>Autenticação e autorização

[**Autenticação**](sql-database-security-overview.md#authentication) é o processo de provar que o usuário é quem ele diz ser. Um usuário se conecta a um banco de dados usando uma conta de usuário.
Quando um usuário tenta se conectar a um banco de dados, ele fornece uma conta de usuário e informações de autenticação. O usuário é autenticado usando um dos dois métodos de autenticação a seguir:

- [Autenticação SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Com este método de autenticação, o usuário envia um nome de conta de usuário e senha associada para estabelecer uma conexão. Essa senha é armazenada no banco de dados principal para contas de usuário vinculadas a um login ou armazenadas no banco de dados contendo a conta de usuário para contas de usuário não vinculadas a um login.
- [Autenticação do Azure Active Directory](sql-database-aad-authentication.md)

  Com este método de autenticação, o usuário envia um nome de conta de usuário e solicita que o serviço use as informações de credenciais armazenadas no Azure Active Directory.

**Logins e usuários**: No Azure SQL, uma conta de usuário em um banco de dados pode ser associada a um login que é armazenado no banco de dados principal ou pode ser um nome de usuário que é armazenado em um banco de dados individual.

- Um **login** é uma conta individual no banco de dados principal, ao qual uma conta de usuário em um ou mais bancos de dados pode ser vinculada. Com um login, as informações de credencial para a conta do usuário são armazenadas com o login.
- Uma **conta de usuário** é uma conta individual em qualquer banco de dados que possa ser, mas não precisa ser vinculada a um login. Com uma conta de usuário que não está vinculada a um login, as informações de credencial são armazenadas na conta do usuário.

[**A autorização**](sql-database-security-overview.md#authorization) para acessar dados e executar várias ações são gerenciadas usando funções de banco de dados e permissões explícitas. A autorização refere-se às permissões atribuídas a um usuário e determina o que esse usuário pode fazer. A autorização é controlada pelas [adesões](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) de função de banco de dados da sua conta de usuário e [permissões de nível de objeto](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Logins e contas de usuário existentes após a criação de um novo banco de dados

Quando você cria sua primeira implantação do Azure SQL, você especifica um login de administrador e uma senha associada para esse login. Esta conta administrativa é chamada **de administração do servidor**. A seguinte configuração de logins e usuários nos bancos de dados mestre e do usuário ocorre durante a implantação:

- Um login SQL com privilégios administrativos é criado usando o nome de login especificado. Um [login](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) é uma conta de usuário individual para fazer logon no Banco de Dados SQL.
- Esse login é concedido permissões administrativas completas em todos os bancos de dados como um [principal de nível de servidor](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Este login tem todas as permissões disponíveis no Banco de Dados SQL e não pode ser limitado. Em uma instância gerenciada, esse login é adicionado à [função servidor fixo sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (essa função não existe com bancos de dados únicos ou agrupados).
- Uma conta `dbo` de [usuário](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) chamada é criada para esse login em cada banco de dados do usuário. O usuário [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) tem todas as permissões de `db_owner` banco de dados no banco de dados e é mapeado para a função de banco de dados fixo. Funções adicionais de banco de dados fixos são discutidas mais tarde neste artigo.

Para identificar as contas do administrador para um banco de dados, abra o portal do Azure e navegue até a guia **Propriedades** do servidor ou instância gerenciada.

![Administradores do SQL Server](media/sql-database-manage-logins/sql-admins.png)

![Administradores do SQL Server](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> O nome de login do admin não pode ser alterado depois de criado. Para redefinir a senha do administrador lógico do servidor, vá para o [portal Azure,](https://portal.azure.com)clique em **Servidores SQL,** selecione o servidor da lista e clique em **Redefinir senha**. Para redefinir a senha de um servidor de instância gerenciado, vá até o portal Dozure, clique na ocorrência e clique **em Redefinir senha**. Você também pode usar o PowerShell ou o Azure CLI.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Crie logins adicionais e usuários com permissões administrativas

Neste ponto, sua ocorrência SQL do Azure só está configurada para acesso usando um único login SQL e conta de usuário. Para criar logins adicionais com permissões administrativas completas ou parciais, você tem as seguintes opções (dependendo do seu modo de implantação):

- **Crie uma conta de administrador do Azure Active Directory com permissões administrativas completas**

  Habilite a autenticação do Azure Active Directory e crie um login de administrador Azure AD. Uma conta do Azure Active Directory pode ser configurada como administradora da implantação do Banco de Dados SQL com permissões administrativas completas. Essa conta pode ser uma conta individual ou de um grupo de segurança. Um administrador Azure AD **deve** ser configurado se você quiser usar contas AD do Azure para se conectar ao Banco de Dados SQL. Para obter informações detalhadas sobre como habilitar a autenticação do Azure AD para todos os tipos de implantação do Banco de Dados SQL, consulte os seguintes artigos:

  - [Use a Autenticação do Azure Active Directory para autenticar com SQL](sql-database-aad-authentication.md)
  - [Configurar e gerenciar autenticação do Azure Active Directory com SQL](sql-database-aad-authentication-configure.md)

- **Em uma implantação de instância gerenciada, crie logins SQL com permissões administrativas completas**

  - Crie um login adicional do SQL Server na instância gerenciada
  - Adicione o login à [função servidor fixo sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) usando a declaração [ALTER SERVER ROLE.](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) Este login terá permissões administrativas completas.
  - Alternativamente, crie [um login Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) usando a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN.</a>

- **Em uma implantação única ou agrupada, crie logins SQL com permissões administrativas limitadas**

  - Crie um login SQL adicional no banco de dados principal para uma implantação de banco de dados único ou pooled ou uma implantação de instância gerenciada
  - Crie uma conta de usuário no banco de dados principal associado a esse novo login
  - Adicione a conta `dbmanager`de `loginmanager` usuário à , `master` a função, ou ambos no banco de dados usando a declaração [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (para Azure Synapse Analytics, use a [declaração sp_addrolemember).](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

  > [!NOTE]
  > `dbmanager`e `loginmanager` as funções **não** dizem respeito a implantações de instâncias gerenciadas.

  Os membros dessas [funções de banco](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) de dados mestre especial para bancos de dados únicos ou agrupados permitem que os usuários tenham autoridade para criar e gerenciar bancos de dados ou para criar e gerenciar logins. Em bancos de dados criados por `dbmanager` um usuário que é membro `db_owner` da função, o membro é mapeado para a função de banco de dados fixo e pode fazer login e gerenciar esse banco de dados usando a conta de `dbo` usuário. Essas funções não têm permissões explícitas fora do banco de dados mestre.

  > [!IMPORTANT]
  > Não é possível criar um login SQL adicional com permissões administrativas completas em um banco de dados único ou pooled.

## <a name="create-accounts-for-non-administrator-users"></a>Criar contas para usuários não administradores

Você pode criar contas para usuários não administrativos usando um dos dois métodos:

- **Crie um login**

  Crie um login SQL no banco de dados mestre. Em seguida, crie uma conta de usuário em cada banco de dados ao qual esse usuário precisa acessar e associe a conta de usuário a esse login. Essa abordagem é preferível quando o usuário deve acessar vários bancos de dados e você deseja manter as senhas sincronizadas. No entanto, essa abordagem tem complexidades quando usada com geo-replicação, pois o login deve ser criado tanto no servidor principal quanto no servidor secundário. Para obter mais informações, consulte [Configurar e gerenciar a segurança do banco de dados Azure SQL para restauração geográfica ou failover](sql-database-geo-replication-security-config.md).
- **Criar uma conta do usuário**

  Crie uma conta de usuário no banco de dados ao qual um usuário precisa de acesso (também chamado de [usuário contido](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Com um banco de dados único ou agrupado, você sempre pode criar esse tipo de conta de usuário.
  - Com um banco de dados de instância gerenciado que não suporta [os princípios do servidor Azure AD,](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)você só pode criar esse tipo de conta de usuário em um banco de dados [contido](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Com uma instância gerenciada que suporta [os princípios do servidor Azure AD,](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)você pode criar contas de usuário para autenticar a instância gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados contido.

  Com essa abordagem, as informações de autenticação do usuário são armazenadas em cada banco de dados e replicadas automaticamente em bancos de dados geo-replicados. No entanto, se a mesma conta existe em vários bancos de dados e você está usando a Autenticação SQL, você deve manter as senhas sincronizadas manualmente. Além disso, se um usuário tem uma conta em diferentes bancos de dados com senhas diferentes, lembrar essas senhas pode se tornar um problema.

> [!IMPORTANT]
> Para criar usuários contidos mapeados para identidades AD do Azure, você deve estar logado usando uma conta Ad do Azure que é um administrador no Banco de Dados SQL. Na instância gerenciada, um login `sysadmin` SQL com permissões também pode criar um login ou usuário do Azure AD.

Para exemplos que mostram como criar logins e usuários, consulte:

- [Crie login para bancos de dados únicos ou agrupados](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Criar login para banco de dados de instância gerenciada](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Crie login para o banco de dados Azure Synapse Analytics](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Criar usuário](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [A criação do Azure AD continha usuários](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Para obter um tutorial de segurança que inclua a criação do SQL Server a usuários contidos em um banco de dados único ou agrupado, consulte [Tutorial: Proteja um banco de dados único ou agrupado](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Usando funções de banco de dados fixos e personalizados

Depois de criar uma conta de usuário em um banco de dados, seja com base em um login ou como um usuário contido, você pode autorizar esse usuário a realizar várias ações e acessar dados em um determinado banco de dados. Você pode usar os seguintes métodos para autorizar o acesso:

- **Funções fixas de banco de dados**

  Adicione a conta de usuário a uma [função de banco de dados fixo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Existem 9 funções de banco de dados fixos, cada uma com um conjunto definido de permissões. As funções de banco de dados fixas mais comuns são: **db_owner,** **db_ddladmin,** **db_datawriter,** **db_datareader,** **db_denydatawriter**e **db_denydatareader.** **db_owner** é usada normalmente para conceder permissão total a apenas alguns usuários. As outras funções fixas de banco de dados são úteis para mover rapidamente um banco de dados simples para desenvolvimento, mas não são recomendadas para a maioria dos bancos de dados de produção. Por exemplo, a **função de** banco de dados fixo db_datareader concede acesso à leitura de todas as tabelas do banco de dados, o que é mais do que estritamente necessário.

  - Para adicionar um usuário a uma função de banco de dados fixo:

    - No Banco de Dados SQL do Azure, use a declaração [ALTER ROLE.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) Por exemplo, consulte [exemplos de ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse Analytics, use a [declaração sp_addrolemember.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) Por exemplo, veja [sp_addrolemember exemplos](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Função de banco de dados personalizada**

  Crie uma função de banco de dados personalizada usando a declaração [CRIAR FUNÇÃO.](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) Uma função personalizada permite que você crie suas próprias funções de banco de dados definidas pelo usuário e conceda cuidadosamente a cada função as menores permissões necessárias para a necessidade da empresa. Em seguida, você pode adicionar os usuários à função personalizada. Quando um usuário for membro de várias funções, ele agregará as permissões de todas elas.
- **Conceder permissões diretamente**

  Conceda as [permissões](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) da conta de usuário diretamente. Há mais de 100 permissões que podem ser concedidas ou negadas individualmente no Banco de Dados SQL. Muitas dessas permissões são aninhadas. Por exemplo, a permissão `UPDATE` em um esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Assim como ocorre na maioria dos sistemas de permissão, a negação de uma permissão substitui uma concessão. Devido à natureza aninhada e ao número de permissões, talvez seja necessário realizar um estudo cuidadoso para criar um sistema de permissões apropriado a fim de proteger corretamente o banco de dados. Comece com a lista de permissões em [Permissões (Mecanismo do Banco de Dados)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) e examine o [gráfico com tamanho de pôster](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) das permissões.

## <a name="using-groups"></a>Using groups

O gerenciamento de acesso eficiente usa permissões atribuídas a grupos de segurança do Active Directory e funções fixas ou personalizadas em vez de usuários individuais.

- Ao usar a autenticação do Azure Active Directory, coloque os usuários do Azure Active Directory em um grupo de segurança do Azure Active Directory. Crie um usuário de banco de dados independente para o grupo. Coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas a esse grupo de usuários.

- Ao usar a autenticação SQL, crie usuários de banco de dados contidos no banco de dados. Coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas a esse grupo de usuários.

  > [!NOTE]
  > Você também pode usar grupos para usuários de banco de dados não contidos.

Você deve estar familiarizado com os seguintes recursos que podem ser usados para limitar ou elevar as permissões:

- [Representação](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e [assinatura de módulo](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) podem ser usadas para elevar permissões temporariamente com segurança.
- [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pode ser usado para limitar quais linhas de um usuário pode acessar.
- [Mascaramento de dados](sql-database-dynamic-data-masking-get-started.md) pode ser usado para limitar a exposição de dados confidenciais.
- [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral de todos os recursos de segurança do Banco de Dados SQL, veja a [visão geral de segurança do SQL](sql-database-security-overview.md).
