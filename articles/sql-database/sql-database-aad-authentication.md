---
title: Azure Active Directory
description: Saiba mais sobre como usar Azure Active Directory para autenticação com Banco de dados SQL, instância gerenciada e análise de Synapse do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 58f40bc7406048df2b052bea799bcbf6466fbbae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80421103"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Use a Autenticação do Azure Active Directory para autenticar com SQL

Azure Active Directory autenticação é um mecanismo de conexão com o [banco de dados SQL](sql-database-technical-overview.md)do Azure, [instância gerenciada](sql-database-managed-instance.md)e [análise de Synapse do Azure (anteriormente SQL data warehouse do Azure)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure Active Directory (AD do Azure). 

> [!NOTE]
> Este artigo se aplica ao SQL Server do Azure e ao banco de dados SQL e ao Azure Synapse. Para simplificar, o banco de dados SQL é usado ao fazer referência ao banco de dados SQL e ao Azure Synapse.

Com a autenticação do Azure AD, é possível gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões. Os benefícios incluem o seguinte:

- Ele fornece uma alternativa à autenticação do SQL Server.
- Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
- Permite o rodízio de senhas em um único lugar
- Os clientes podem gerenciar permissões de banco de dados usando grupos (Azure AD) externos.
- Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Active Directory do Azure dá suporte.
- A autenticação do Azure AD usa usuários de banco de dados independente para autenticar identidades no banco de dados.
- O Azure AD dá suporte à autenticação baseada em token em aplicativos que se conectam ao Banco de Dados SQL.
- A autenticação do Azure AD dá suporte a:
  - Identidades somente na nuvem do Azure AD
  - Identidades híbridas do Azure AD que dão suporte a:
    - Autenticação de nuvem com duas opções combinadas com SSO (logon único) contínuo: autenticação de **passagem** e autenticação de **hash de senha**
    - Autenticação federada
  - Para obter mais informações sobre os métodos de autenticação do Azure AD e qual deles escolher, consulte o seguinte artigo:
    - [Escolha o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory](../active-directory/hybrid/choose-ad-authn.md)
- O Azure AD dá suporte a conexões do SQL Server Management Studio que usam a Autenticação Universal do Active Directory, que inclui o MFA (Autenticação Multifator).  A MFA inclui autenticação eficiente com uma variedade de opções de verificação fáceis como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis. Para obter mais informações, consulte [suporte do SSMS para MFA do Azure AD com o banco de dados SQL e Azure Synapse](sql-database-ssms-mfa-authentication.md)
- O Azure AD dá suporte a conexões semelhantes do SSDT (SQL Server Data Tools) que usa a Autenticação Interativa do Active Directory. Para obter mais informações, consulte [suporte a Azure Active Directory no SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Não há suporte para conectar ao SQL Server em execução em uma VM do Azure usando uma conta do Azure Active Directory. Use um conta de domínio do Active Directory.  

As etapas de configuração incluem os procedimentos a seguir para configurar e usar a autenticação do Active Directory do Azure.

1. Criar e popular o Azure AD.
2. Opcional: associe ou altere o Active Directory que está associado atualmente à sua Assinatura do Azure.
3. Crie um administrador de Azure Active Directory para o servidor do banco de dados SQL do Azure, a instância gerenciada ou o [Azure Synapse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configure os computadores cliente.
5. Crie usuários de banco de dados independente em seu banco de dados, mapeados para identidades do Azure AD.
6. Conecte-se ao banco de dados usando identidades do Azure AD.

> [!NOTE]
> Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o banco de dados SQL do Azure, instância gerenciada e Synapse do Azure, consulte [Configurar o Azure AD com o banco de dados SQL do Azure](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Confiar na arquitetura

- Para dar suporte à senha de usuário nativo do Azure AD, será considerada apenas a parte da Nuvem e o Azure AD/Banco de Dados SQL do Azure.
- Para dar suporte a credenciais de logon único do Windows (ou usuário/senha para credencial do Windows), use Azure Active Directory credenciais de um domínio federado ou gerenciado configurado para logon único contínuo para autenticação de passagem e hash de senha. Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](../active-directory/hybrid/how-to-connect-sso.md).
- Para dar suporte à Autenticação federada (ou a usuário/senha para as credenciais do Windows), será necessária a comunicação com o bloco do ADFS.

Para obter mais informações sobre identidades híbridas do Azure AD, a instalação e a sincronização, consulte os seguintes artigos:

- Autenticação de hash de senha- [implementar a sincronização de hash de senha com sincronização de Azure ad Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticação de passagem – [Azure Active Directory autenticação de passagem](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticação federada – [Implantando serviços de Federação do Active Directory (AD FS) no Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure ad Connect e Federação](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Para obter um exemplo de autenticação federada com a infraestrutura do ADFS (ou usuário/senha para credenciais do Windows), consulte o diagrama a seguir. As setas indicam caminhos para comunicação.

![diagrama de autenticação do aad][1]

O diagrama a seguir indica as relações de federação, confiança e hospedagem que permitem que um cliente se conecte a um banco de dados enviando um token. O token é autenticado pelo Azure AD e é considerado confiável pelo banco de dados. O Cliente 1 pode representar um Azure Active Directory com usuários nativos ou um Azure AD com usuários federados. O Cliente 2 representa uma solução possível, incluindo os usuários importados; neste exemplo, provenientes de um Azure Active Directory federado com o ADFS sendo sincronizado com o Azure Active Directory. É importante entender que o acesso a um banco de dados com a autenticação do Azure AD exige que a assinatura de hospedagem esteja associada ao Azure AD. A mesma assinatura deve ser usada para criar o SQL Server hospedando o banco de dados SQL do Azure ou o Azure Synapse.

![relação de assinatura][2]

## <a name="administrator-structure"></a>Estrutura do administrador

Ao usar a autenticação do Azure AD, há duas contas de administrador para o servidor do banco de dados SQL e a instância gerenciada; o administrador de SQL Server original e o administrador do Azure AD. Os mesmos conceitos se aplicam ao Azure Synapse. Somente o administrador com base em uma conta do AD do Azure pode criar o primeiro usuário de banco de dados do AD do Azure contido em um banco de dados de usuário. O logon de administrador do AD do Azure pode ser um usuário ou um grupo do AD do Azure. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, permitindo múltiplos administradores do AD do Azure para a instância do SQL Server. Usar a conta de grupo como um administrador aprimora a capacidade de gerenciamento, permitindo que você adicione e remova membros do grupo no AD do Azure centralmente, sem alterar os usuários ou permissões no Banco de Dados SQL. Somente um administrador do AD do Azure (um usuário ou grupo) pode ser configurado por vez, a qualquer momento.

![estrutura de administrador][3]

## <a name="permissions"></a>Permissões

Para criar novos usuários, você deve ter a permissão `ALTER ANY USER` no banco de dados. A permissão `ALTER ANY USER` pode ser concedida a qualquer usuário do banco de dados. A permissão `ALTER ANY USER` também é mantida pelas contas de administrador do servidor e usuários de banco de dados com a permissão `CONTROL ON DATABASE` ou `ALTER ON DATABASE` para esse banco de dados e por membros da função de banco de dados `db_owner`.

Para criar um usuário de banco de dados independente no banco de dados SQL do Azure, instância gerenciada ou Azure Synapse, você deve se conectar ao banco de dados ou instância usando uma identidade do Azure AD. Para criar o primeiro usuário de banco de dados independente, você deve se conectar ao banco de dados usando o administrador do AD do Azure (que é o proprietário do banco de dados). Isso é demonstrado em [configurar e gerenciar a autenticação Azure Active Directory com o banco de dados SQL ou o Azure Synapse](sql-database-aad-authentication-configure.md). Qualquer autenticação do Azure AD só será possível se o administrador do Azure AD tiver sido criado para o banco de dados SQL do Azure ou o Azure Synapse. Se o administrador do Azure Active Directory tiver sido removido do servidor, os usuários existentes do Azure Active Directory criados anteriormente dentro do SQL Server não poderão mais se conectar ao banco de dados usando suas credenciais do Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Limitações e recursos do AD do Azure

- Os seguintes membros do Azure AD podem ser provisionados no Azure SQL Server ou no Azure Synapse:

  - Membros nativos: Membro criado no Microsoft Azure Active Directory no domínio gerenciado ou em um domínio do cliente. Para saber mais, confira [Adicionar seu nome de domínio ao Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Membros de um domínio Active Directory federado com Azure Active Directory em um domínio gerenciado configurado para logon único contínuo com passagem ou autenticação de hash de senha. Para obter mais informações, consulte [Microsoft Azure agora dá suporte à Federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) e [Azure Active Directory logon único contínuo](../active-directory/hybrid/how-to-connect-sso.md).
  - Membros importados de outros Azure ADs que são membros de domínio nativo ou federado.
  - Grupos do Active Directory criados como grupos de segurança.

- Os usuários do Azure AD que fazem parte de um grupo `db_owner` que tem a função de servidor não podem usar a sintaxe de **[credencial CREATE DATABASE no escopo](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** no banco de dados SQL do Azure e no Azure Synapse. Você verá o seguinte erro:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Conceda a função `db_owner` diretamente ao usuário individual do Azure AD para atenuar o problema de **CREATE DATABASE SCOPED CREDENTIAL**.

- Estas funções do sistema retornam valores NULL quando executadas em entidades de segurança do Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Instâncias gerenciadas

- As entidades de segurança do servidor do Azure AD (logons) e os usuários têm suporte como um recurso de visualização para [instâncias gerenciadas](sql-database-managed-instance.md).
- A definição de entidades de segurança do Azure AD (logons) mapeados para um grupo do Azure AD como proprietário do banco de dados não tem suporte em [instâncias gerenciadas](sql-database-managed-instance.md).
    - Uma extensão disso é que, quando um grupo é adicionado como parte da função `dbcreator` de servidor, os usuários desse grupo podem se conectar à instância gerenciada e criar novos bancos de dados, mas não poderão acessar o banco. Isso ocorre porque o novo proprietário do banco de dados é SA e não o usuário do Azure AD. Esse problema não se manifesta se o usuário individual é adicionado à função de servidor `dbcreator`.
- A execução de gerenciamento e trabalhos do SQL Agent tem suporte para entidades de segurança do servidor do Azure AD (logons).
- As operações de restauração e backup do banco de dados podem ser executadas pelas entidades de segurança do servidor do Azure AD (logons).
- A auditoria de todas as instruções relacionadas às entidades de segurança do servidor do Azure AD (logons) e aos eventos de autenticação tem suporte.
- Há suporte para a conexão de administrador dedicada para as entidades de segurança do servidor do Azure AD (logons) que são membros da função do servidor sysadmin.
    - Compatível por meio do utilitário sqlcmd e do SQL Server Management Studio.
- Os gatilhos de logon têm suporte para os eventos de logon que vêm das entidades de segurança do servidor do Azure AD (logons).
- Os emails do Service Broker e do banco de dados podem ser configurados usando uma entidade de segurança do servidor do Azure AD (logon).


## <a name="connecting-using-azure-ad-identities"></a>Conectar-se usando as identidades do Azure AD

A autenticação do Active Directory do Azure dá suporte aos seguintes métodos de conexão a um banco de dados usando identidades do AD do Azure:

- Senha do Azure Active Directory
- Integrada do Azure Active Directory
- Universal do Azure Active Directory com o MFA
- Uso da autenticação de token do aplicativo

Os seguintes métodos de autenticação têm suporte para entidades de segurança do servidor do Azure AD (logons):

- Senha do Azure Active Directory
- Integrada do Azure Active Directory
- Universal do Azure Active Directory com o MFA


### <a name="additional-considerations"></a>Considerações adicionais

- Para aumentar a capacidade de gerenciamento, é recomendável que você provisione um grupo dedicado do Microsoft Azure AD como administrador.   
- Somente um administrador do Azure AD (um usuário ou grupo) pode ser configurado para um servidor de banco de dados SQL do Azure ou Synapse do Azure a qualquer momento.
  - A adição de entidades de segurança de servidor do Azure AD (logons) para instâncias gerenciadas permite a possibilidade de criar várias entidades de segurança de servidor do Azure AD (logons `sysadmin` ) que podem ser adicionadas à função.
- Somente um administrador do Azure AD para SQL Server pode inicialmente se conectar ao servidor do banco de dados SQL do Azure, instância gerenciada ou Azure Synapse usando uma conta de Azure Active Directory. O administrador do Active Directory pode configurar os próximos usuários do banco de dados do Azure AD.   
- É recomendável configurar o tempo limite da conexão para 30 segundos.   
- O SQL Server 2016 Management Studio e o SQL Server Data Tools para Visual Studio 2015 (versão 14.0.60311.1 de abril de 2016 ou posterior) dão suporte à autenticação do Azure Active Directory. (Há suporte para a autenticação do Azure AD no **Provedor de Dados .NET Framework para SqlServer**; é necessária, no mínimo, a versão 4.6 do .NET Framework). Portanto, as versões mais recentes dessas ferramentas e desses aplicativos da camada de dados (DAC e .BACPAC) podem usar a autenticação do Azure AD.   
- Na versão 15.0.1 em diante, o [utilitário sqlcmd](/sql/tools/sqlcmd-utility) e o [utilitário bcp](/sql/tools/bcp-utility) dão suporte à autenticação Interativa do Active Directory com o MFA.
- O SQL Server Data Tools para Visual Studio 2015 requer pelo menos a versão de abril de 2016 do Data Tools (versão 14.0.60311.1). Atualmente, os usuários do Azure AD não são mostrados no Pesquisador de Objetos do SSDT. Como alternativa, exiba os usuários em [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- O [Microsoft JDBC Driver 6.0 para SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) dá suporte à autenticação do Azure AD. Confira também [Configuração das propriedades de conexão](https://msdn.microsoft.com/library/ms378988.aspx).   
- O PolyBase não pode ser autenticado com a autenticação do Azure AD.   
- Há suporte para a autenticação do Azure AD no Banco de Dados SQL por meio das folhas **Importar Banco de Dados** e **Exportar Banco de Dados** do portal do Azure. Também há suporte para importação e exportação com a autenticação do Azure AD no comando do PowerShell.   
- A autenticação do Azure AD tem suporte para o banco de dados SQL, a instância gerenciada e o Azure Synapse com o uso da CLI. Para obter mais informações, consulte [configurar e gerenciar a autenticação de Azure Active Directory com o banco de dados SQL ou o Azure Synapse](sql-database-aad-authentication-configure.md) e [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o banco de dados SQL do Azure ou o Azure Synapse, consulte [configurar e gerenciar a autenticação de Azure Active Directory com o banco de dados SQL, instância gerenciada ou Synapse do Azure](sql-database-aad-authentication-configure.md).
- Para obter um tutorial de como usar entidades de segurança de servidor do Azure AD (logons) com instâncias gerenciadas, consulte [entidades de segurança do Azure Ad Server (logons) com instâncias gerenciadas](sql-database-managed-instance-aad-security-tutorial.md)
- Para obter uma visão geral de logons, usuários, funções de banco de dados e permissões no banco de dados SQL, consulte [logons, usuários, funções de banco de dados e permissões](sql-database-manage-logins.md).
- Para obter mais informações sobre objetos de banco de dados, confira [Entidades](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de banco de dados, confira [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121.aspx).
- Para obter a sintaxe sobre a criação de entidades de segurança do servidor do Azure AD (logons) para instâncias gerenciadas, consulte [criar logon](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Para obter mais informações sobre as regras de firewall no Banco de Dados SQL, confira [Regras de firewall de Banco de Dados SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
