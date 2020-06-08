---
title: Autorizar o acesso ao servidor e ao banco de dados usando logons e contas de usuário
description: Saiba mais sobre como o Banco de Dados SQL do Azure e o Azure Synapse Analytics autenticam usuários para acesso usando logons e contas de usuário. Saiba também como funções do banco de dados e permissões explícitas para autorizar logons e usuários para executar ações e consultar dados.
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
ms.openlocfilehash: 751c85559330272e84e628d22756d47c24b08711
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701653"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Como autorizar o acesso ao banco de dados para usuários autenticados no Banco de Dados SQL e no Azure Synapse Analytics usando logons e contas de usuário

Neste artigo, você aprenderá sobre:

- Opções para configurar o Banco de Dados SQL do Azure e o Azure Synapse Analytics (anteriormente SQL Data Warehouse do Azure) para permitir que os usuários executem tarefas administrativas e acessem os dados armazenados nesses bancos de dados.
- A configuração de acesso e autorização após criar inicialmente um novo Banco de Dados SQL do Azure
- Como adicionar logons e contas de usuário no banco de dados mestre e conceder permissões administrativas a essas contas
- Como adicionar contas de usuário em bancos de dados de usuário associadas a logons ou a contas de usuário independentes
- Configurar contas de usuário com permissões nos bancos de dados do usuário usando funções de banco de dados e permissões explícitas

> [!IMPORTANT]
> Bancos de dados no Banco de Dados SQL do Azure e no Azure Synapse Analytics (anteriormente SQL Data Warehouse do Azure) são mencionados coletivamente no restante deste artigo, como bancos de dados ou como SQL do Azure (para simplificar).

## <a name="authentication-and-authorization"></a>Autenticação e autorização

A [**autenticação**](sql-database-security-overview.md#authentication) é o processo de provar que o usuário é quem diz ser. Um usuário se conecta a um banco de dados com uma conta de usuário.
Uma conta de usuário e informações de autenticação são fornecidas quando um usuário tenta se conectar a um banco de dados. O usuário é autenticado usando um dos dois métodos de autenticação a seguir:

- [Autenticação SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  O usuário envia um nome de conta de usuário e uma senha associada para estabelecer uma conexão ao usar esse método de autenticação. Essa senha é armazenada no banco de dados mestre das contas de usuário vinculadas a um logon ou é armazenada no banco de dados que contém as contas de usuário *não* vinculadas a um logon.
- [Autenticação do Azure Active Directory](sql-database-aad-authentication.md)

  O usuário envia um nome de conta de usuário e solicita que o serviço use as informações de credenciais armazenadas no Azure Active Directory ao usar esse método de autenticação.

**Logons e usuários**: No SQL do Azure, uma conta de usuário em um banco de dados pode ser associada a um logon que é armazenado no banco de dados mestre ou pode ser um nome de usuário que é armazenado em um banco de dados individual.

- Um **logon** é uma conta individual no banco de dados mestre para a qual uma conta de usuário pode ser vinculada em um ou mais bancos de dados. Com um logon, as informações de credencial para a conta de usuário são armazenadas com o logon.
- Uma **conta de usuário** é uma conta individual em qualquer banco de dados que pode estar, mas não precisa estar vinculada a um logon. Com uma conta de usuário que não está vinculada a um logon, as informações da credencial são armazenadas com a conta de usuário.

A [**autorização**](sql-database-security-overview.md#authorization) para acessar dados e executar várias ações são gerenciadas usando funções de banco de dados e permissões explícitas. A autorização refere-se às permissões atribuídas a um usuário e determina o que o usuário tem permissão para fazer. A autorização é controlada pelas [associações de função](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) e [permissões no nível do objeto](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) do banco de dados da conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Logons existentes e contas de usuário após criar um novo banco de dados

Ao criar sua primeira implantação do SQL do Azure, você especifica um logon de administrador e uma senha associada a esse logon. Essa conta administrativa é chamada de **Administrador do servidor**. A seguinte configuração de logons e de usuários nos bancos de dados mestre e de usuário ocorre durante a implantação:

- Um logon do SQL com privilégios de administrador é criado usando o nome de logon especificado. Um [logon](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) é uma conta de usuário individual para fazer logon no Banco de Dados SQL.
- Esse logon recebe permissões administrativas completas em todos os bancos de dados como uma [entidade de segurança no nível do servidor](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Este logon tem todas as permissões disponíveis no Banco de Dados SQL e não pode ser limitado. Em uma instância gerenciada, esse logon é adicionado à [função do servidor fixo sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (essa função não existe com bancos de dados únicos ou em pool).
- Uma [conta de usuário](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) chamada `dbo` é criada para esse logon em cada banco de dados de usuário. O usuário [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) tem todas as permissões de banco de dados no banco de dados e é mapeado para a função de banco de dados fixa `db_owner`. Funções de banco de dados fixa adicionais são discutidas posteriormente neste artigo.

Para identificar as contas de administrador de um banco de dados, abra o portal do Azure e vá até a guia **Propriedades** do seu servidor ou instância gerenciada.

![Administradores do SQL Server](media/sql-database-manage-logins/sql-admins.png)

![Administradores do SQL Server](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> O nome do logon do administrador não pode ser alterado após ter sido criado. Para redefinir a senha para o administrador do servidor lógico, vá para o [portal do Azure](https://portal.azure.com), clique em **SQL Servers**, selecione o servidor na lista e clique em **Redefinir senha**. Vá para o portal do Azure, clique na instância e clique em **Redefinir senha** para redefinir a senha de um servidor de instância gerenciada. Você também pode usar o PowerShell ou a CLI do Azure.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Crie logons e usuários adicionais com permissões administrativas

Neste ponto, a instância do SQL do Azure só é configurada para acesso usando uma única conta de usuário e logon do SQL. Você tem as seguintes opções (dependendo do seu modo de implantação) para criar logons adicionais com permissões administrativas totais ou parciais:

- **Criar uma conta de administrador do Azure Active Directory com permissões administrativas totais**

  Habilite a autenticação do Azure Active Directory e crie um logon de administrador do Azure AD. Uma conta do Azure Active Directory pode ser configurada como um administrador da implantação do Banco de Dados SQL com permissões administrativas totais. Essa conta pode ser uma conta de grupo de segurança ou individual. Um administrador do Azure AD **deve** ser configurado se você quiser usar as contas do Azure AD para se conectar ao Banco de Dados SQL. Para obter informações detalhadas sobre como habilitar a autenticação do Azure AD para todos os tipos de implantação do Banco de Dados SQL, consulte os seguintes artigos:

  - [Usar a Autenticação do Azure Active Directory para autenticação com SQL](sql-database-aad-authentication.md)
  - [Configurar e gerenciar autenticação do Azure Active Directory com SQL](sql-database-aad-authentication-configure.md)

- **Em uma implantação de instância gerenciada, crie logons de SQL com permissões administrativas totais**

  - Criar um logon do SQL Server adicional na instância gerenciada
  - Adicione o logon à [função de servidor fixa sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) usando a instrução [ALTERAR FUNÇÃO DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql). Este logon terá permissões administrativas totais.
  - Crie um [logon do Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) usando a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CRIAR LOGON</a> como alternativa.

- **Em uma implantação única ou em pool, crie logons de SQL com permissões administrativas limitadas**

  - Criar um logon do SQL adicional no banco de dados mestre para uma implantação de banco de dados única ou em pool, ou uma implantação de instância gerenciada
  - Criar uma conta de usuário no banco de dados mestre associado ao novo logon
  - Adicione a conta de usuário ao `dbmanager`, à função `loginmanager` ou a ambos no banco de dados `master` usando a instrução [ALTERAR FUNÇÃO DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (para o Azure Synapse Analytics, use a instrução [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)).

  > [!NOTE]
  > As funções `dbmanager` e `loginmanager` **não** pertencem a implantações de instância gerenciada.

  Os membros dessas [funções de banco de dados mestre especiais](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) para bancos de dados únicos ou em pool permitem que os usuários tenham autoridade para criar e gerenciar bancos de dados ou criar e gerenciar logons. Em bancos de dados criados por um usuário que seja membro da função `dbmanager`, o membro é mapeado na função de banco de dados fixa `db_owner` e pode fazer logon e gerenciar esse banco de dados usando a conta de usuário do `dbo`. Essas funções não têm permissões explícitas fora do banco de dados mestre.

  > [!IMPORTANT]
  > Você não pode criar um logon do SQL adicional com permissões administrativas totais em um banco de dados único ou em pool.

## <a name="create-accounts-for-non-administrator-users"></a>Crie contas para usuários não administradores

Você pode criar contas para usuários não administradores usando um dos dois métodos:

- **Criar um logon**

  Crie um logon do SQL no banco de dados mestre. Depois crie uma conta de usuário em cada banco de dados ao qual o usuário precisa acessar e associe a conta de usuário a esse logon. Essa abordagem é preferida quando o usuário deve acessar vários bancos de dados e se você quiser manter as senhas sincronizadas. No entanto, essa abordagem tem complexidades quando usadas com replicação geográfica, já que o logon deve ser criado no servidor primário e no(s) servidor(es) secundário(s). Para obter mais informações, consulte [Configurar e gerenciar a segurança do Banco de Dados SQL do Azure para restauração geográfica ou failover ](sql-database-geo-replication-security-config.md).
- **Criar uma conta de usuário**

  Crie uma conta de usuário no banco de dados para a qual um usuário precisa de acesso (também chamado de [usuário independente](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Você sempre pode criar esse tipo de conta de usuário com um banco de dados único ou em pool.
  - Com um banco de dados de instância gerenciada que não dá suporte a [entidades de segurança do servidor do Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), você só pode criar esse tipo de conta de usuário em um [banco de dados independente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Com a instância gerenciada que suporta [entidades de segurança do servidor do Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), você pode criar contas de usuário para autenticar na instância gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados independente.

  Com essa abordagem, as informações de autenticação de usuário são armazenadas em cada banco de dados e replicadas automaticamente nos bancos de dados replicados geograficamente. No entanto, se a mesma conta existir em vários bancos de dados e você estiver usando a Autenticação do SQL, você deverá manter as senhas sincronizadas manualmente. Além disso, se um usuário tiver uma conta em bancos de dados diferentes com senhas diferentes, lembrar dessas senhas pode se tornar um problema.

> [!IMPORTANT]
> Você deve estar conectado usando uma conta do Azure AD que seja um administrador no banco de dados SQL para criar usuários independentes mapeados para identidades do Azure AD. Na instância gerenciada, um logon do SQL com `sysadmin` permissões também pode criar um logon ou usuário do Azure AD.

Para obter exemplos de criar logons e usuários, consulte:

- [Criar logon para bancos de dados únicos ou em pool](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Criar logon para banco de dados de instância gerenciada](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Criar logon para banco de dados do Azure Synapse Analytics](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Criar usuário](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Criando usuários independentes do Azure AD](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Para obter um tutorial sobre segurança que inclui a criação de SQL Server um usuário independente em um banco de dados individual ou em pool, consulte o [Tutorial: proteger um banco de dados individual ou em pool](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Usando funções de banco de dados fixas e personalizadas

Após criar uma conta de usuário em um banco de dados, seja com base em um logon ou como um usuário independente, você pode autorizar esse usuário a executar várias ações e acessar dados em um banco de dados em particular. Você pode usar os seguintes métodos para autorizar o acesso:

- **Funções de banco de dados fixa**

  Adicione a conta do usuário a uma [função de banco de dados fixa](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Há 9 funções de banco de dados fixa, cada uma com um conjunto definido de permissões. As funções de banco de dados fixa mais comuns são: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** e **db_denydatareader**. **db_owner** é usada normalmente para conceder permissão total a apenas alguns usuários. As outras funções fixas de banco de dados são úteis para mover rapidamente um banco de dados simples para desenvolvimento, mas não são recomendadas para a maioria dos bancos de dados de produção. Por exemplo, a função de banco de dados fixa **db_datareader** concede acesso de leitura a todas as tabelas no banco de dados, sendo mais do que é estritamente necessário.

  - Para adicionar um usuário a uma função de banco de dados fixa:

    - No Banco de Dados SQL do Azure, use a instrução [ALTERAR FUNÇÃO](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql). Para obter exemplos, consulte [Exemplos de ALTERAR FUNÇÃO](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Para o Azure Synapse Analytics, use a instrução [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Para obter exemplos, consulte [Exemplos de sp_addrolemember](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Função de banco de dados personalizada**

  Crie uma função de banco de dados personalizada usando a instrução [CRIAR FUNÇÃO](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql). Uma função personalizada permite criar suas próprias funções de banco de dados definidas pelo usuário e conceder cuidadosamente a cada função o mínimo de permissões necessárias para a necessidade de negócios. Você pode adicionar usuários à função personalizada. Quando um usuário for membro de várias funções, ele agregará as permissões de todas elas.
- **Conceder permissões diretamente**

  Conceda [permissões](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) à conta de usuário diretamente. Há mais de 100 permissões que podem ser concedidas ou negadas individualmente no Banco de Dados SQL. Muitas dessas permissões são aninhadas. Por exemplo, a permissão `UPDATE` em um esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Assim como ocorre na maioria dos sistemas de permissão, a negação de uma permissão substitui uma concessão. Devido à natureza aninhada e ao número de permissões, talvez seja necessário realizar um estudo cuidadoso para criar um sistema de permissões apropriado a fim de proteger corretamente o banco de dados. Comece com a lista de permissões em [Permissões (Mecanismo do Banco de Dados)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) e examine o [gráfico com tamanho de pôster](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) das permissões.

## <a name="using-groups"></a>Usando grupos

O gerenciamento de acesso eficiente usa permissões atribuídas a grupos de segurança do Active Directory e funções fixas ou personalizadas em vez de usuários individuais.

- Ao usar a autenticação do Azure Active Directory, coloque os usuários do Azure Active Directory em um grupo de segurança do Azure Active Directory. Crie um usuário de banco de dados independente para o grupo. Coloque um ou mais usuários do banco de dados em uma função de banco de dados personalizada com permissões específicas adequadas para esse grupo de usuários.

- Ao usar a autenticação SQL, crie usuários de banco de dados independentes no banco de dados. Coloque um ou mais usuários do banco de dados em uma função de banco de dados personalizada com permissões específicas adequadas para esse grupo de usuários.

  > [!NOTE]
  > Você também pode usar grupos para usuários de banco de dados dependentes.

Você deve estar familiarizado com os seguintes recursos que podem ser usados para limitar ou elevar as permissões:

- [Representação](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e [assinatura de módulo](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) podem ser usadas para elevar permissões temporariamente com segurança.
- [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pode ser usado para limitar quais linhas de um usuário pode acessar.
- [Mascaramento de dados](sql-database-dynamic-data-masking-get-started.md) pode ser usado para limitar a exposição de dados confidenciais.
- [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral de todos os recursos de segurança do Banco de Dados SQL, veja a [visão geral de segurança do SQL](sql-database-security-overview.md).
