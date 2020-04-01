---
title: Configurar a autenticação do Azure Active Directory
description: Aprenda a se conectar ao Banco de Dados SQL, instância gerenciada e Análise sinapse do Azure usando a autenticação do Azure Active Directory - depois de configurar o Azure AD.
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
ms.openlocfilehash: 0e244ea185011bbb7d9f0facad399bb9b577bbc2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419884"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configurar e gerenciar autenticação do Azure Active Directory com SQL

Este artigo mostra como criar e preencher o Azure AD e, em seguida, usar o Azure AD com o Azure [SQL Database (SQL DB),](sql-database-technical-overview.md) [instância gerenciada (MI)](sql-database-managed-instance.md)e [Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral, consulte [Autenticação do Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Este artigo se aplica ao servidor Azure SQL e ao SQL Database e ao Azure Synapse. Para simplificar, o Banco de Dados SQL é usado quando se refere tanto ao Banco de Dados SQL quanto ao Sinapse Azure.

> [!IMPORTANT]  
> Não há suporte para conectar ao SQL Server em execução em uma VM do Azure usando uma conta do Azure Active Directory. Use um conta de domínio do Active Directory.

## <a name="azure-ad-authentication-methods"></a>Métodos de autenticação do Azure AD

A autenticação Azure AD suporta os seguintes métodos de autenticação:

- Identidades somente em nuvem do Azure AD
- Identidades híbridas Azure AD que suportam:
  - Autenticação em nuvem com duas opções acopladas a um único sinal (SSO)
    - Autenticação de hash de senha do Azure AD
    - Autenticação de passagem do Azure AD
  - Autenticação federada

Para obter mais informações sobre os métodos de autenticação do Azure AD e qual escolher, veja o artigo:
- [Escolha o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory](../active-directory/hybrid/choose-ad-authn.md)

Para obter mais informações sobre as identidades híbridas do Azure AD, a configuração e a sincronização, consulte os seguintes artigos:

- Autenticação de hash de senha - [Implementar sincronização de hash de senha com sincronização do Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticação de passagem - [Autenticação de Passagem do Diretório Ativo do Azure](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticação federada - [Implantação de Serviços de Federação de Diretórios Ativos no Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure AD Connect e federação](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Todos os métodos de autenticação acima são suportados para SQL DB (pools de banco de dados e banco de dados único), instância gerenciada e Sinapse Azure.

## <a name="create-and-populate-an-azure-ad"></a>Criar e popular um Azure AD

Crie um Azure AD e popule-o com usuários e grupos. O Azure AD pode ser o domínio gerenciado pelo Azure AD inicial. O Azure AD também pode ser um Active Directory Domain Services local federado com o Azure AD.

Para obter mais informações, consulte [Integrando suas identidades locais no Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Adicionar seu próprio nome de domínio ao Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório do Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Gerenciar o Azure AD usando o Windows PowerShell](/powershell/azure/overview) e [Portas e protocolos necessários para a identidade híbrida](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma assinatura do Azure ao Azure Active Directory

1. Associe sua assinatura do Azure ao Azure Active Directory tornando o diretório um diretório confiável para a assinatura do Azure que hospeda o banco de dados. Para mais detalhes, consulte [Como as assinaturas do Azure são associadas ao Microsoft Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Use o seletor de diretório no portal do Azure para alternar para a assinatura associada ao domínio.

   > [!IMPORTANT]
   > Cada assinatura do Azure tem uma relação de confiança com uma instância do Azure AD. Isso significa que ela confia que esse diretório autentique usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma única assinatura confia em apenas um diretório. Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante), que são mais similares a recursos filho de uma assinatura. Se uma assinatura expira, o acesso a esses outros recursos associados à assinatura também pára. Mas o diretório permanece no Azure e você pode associar outra assinatura a ele, além de continuar a gerenciar os usuários do diretório. Para obter mais informações sobre recursos, consulte [Noções básicas sobre o acesso a recursos no Azure](../active-directory/active-directory-b2b-admin-add-users.md). Para saber mais sobre essa relação de confiança, consulte relação [Como associar ou adicionar uma assinatura do Azure ao Microsoft Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador do Azure AD para o Azure SQL Server

Cada servidor SQL do Azure (que hospeda um Banco de Dados SQL ou Synapse Do Zure) começa com uma única conta de administrador de servidor que é a administradora de todo o servidor Azure SQL. Um segundo administrador do SQL Server deve ser criado, que é uma conta do Azure AD. Essa entidade de segurança é criada como um usuário de banco de dados independente no banco de dados mestre. Como administradores, as contas de administrador do servidor são membros da função **db_owner** em todos os bancos de dados de usuários e inserem cada banco de dados de usuário como o usuário **dbo**. Para obter mais informações sobre as contas do administrador do servidor, veja [Gerenciando Bancos de Dados e Logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

Ao usar o Azure Active Directory com a Replicação Geográfica, o administrador do Azure Active Directory deverá ser configurado para os servidores primários e secundários. Se um servidor não tiver um administrador do Azure Active Directory, os usuários e logons do Azure Active Directory obterão um erro de servidor "Não é possível conectar-se".

> [!NOTE]
> Usuários que não são baseados em uma conta do Azure AD (incluindo a conta de administrador do Azure SQL Server) não podem criar usuários baseados no Azure AD porque eles não têm permissão para validar os usuários do banco de dados proposto com o Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Provisão um administrador do Azure Active Directory para sua instância gerenciada

> [!IMPORTANT]
> Só siga estas etapas se você estiver provisionando uma instância gerenciada. Esta operação só pode ser executada pelo administrador global/empresa ou por um administrador de função privilegiado no Azure AD. As etapas a seguir descrevem o processo de concessão de permissões para usuários com privilégios diferentes no diretório.

> [!NOTE]
> Para administradores Azure AD para MI criados antes do GA, mas continue operando após GA, não há alteração funcional do comportamento existente. Para obter mais informações, consulte a funcionalidade de administrador do [Azure AD](#new-azure-ad-admin-functionality-for-mi) para a seção MI para obter mais detalhes.

Sua instância gerenciada precisa de permissões para ler o Azure AD para realizar com sucesso tarefas como autenticação de usuários por meio de membros de grupos de segurança ou criação de novos usuários. Para que isso funcione, você precisa conceder permissões para a instância gerenciada para ler o Azure AD. Há duas maneiras de fazer isso: no Portal e no PowerShell. As etapas a seguir mostram os dois métodos.

1. No portal do Azure, no canto superior direito, selecione a conexão para exibir uma lista suspensa dos Active Directories possíveis.

2. Escolha o Active Directory correto como o AD do Azure padrão.

   Esta etapa vincula a assinatura associada ao Active Directory à Instância Gerenciada, garantindo que a mesma assinatura é usada para o Azure AD e a Instância Gerenciada.

3. Navegue para a Instância Gerenciada e selecione uma que você deseja usar para a integração do Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Selecione a faixa na parte superior da página de administração do Active Directory e conceda permissão ao usuário atual. Se você estiver logado como administrador global/da empresa no Azure AD, você pode fazê-lo a partir do portal Azure ou usando o PowerShell com o script abaixo.

    ![grant permissions-portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Depois que a operação for concluída com êxito, a seguinte notificação será exibida no canto superior direito:

    ![sucesso](./media/sql-database-aad-authentication/success.png)

6. Agora você pode escolher o administrador do Azure AD para sua instância gerenciada. Para isso, na página de administração do Active Directory, selecione o comando **Definir administrador**.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na página de administração do AAD, pesquise um usuário, selecione o usuário ou grupo para ser um administrador e, em seguida, escolha **Selecionar**.

   A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Usuários ou grupos que estejam esmaecidos não poderão ser selecionados porque não têm suporte como administradores do Azure AD. Consulte a lista de administradores com suporte em [Limitações e recursos do Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). O RBAC (controle de acesso baseado em função) aplica-se somente ao portal do Azure e não é propagado para o SQL Server.

    ![Adicionar administrador do Diretório Ativo do Azure](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. Na parte superior da página Administrador do Active Directory, selecione **Salvar**.

    ![Salvar](./media/sql-database-aad-authentication/save.png)

    O processo de alteração do administrador pode levar vários minutos. Em seguida, o novo administrador é exibido na caixa de administração do Active Directory.

Depois de provisionar um administrador azure AD para sua instância gerenciada, você pode começar a criar os principais (logins) do servidor Azure AD com a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN.</a> Para obter mais informações, consulte [visão geral da instância gerenciada](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Para remover um Administrador mais tarde, na parte superior da página Administrador do Active Directory, selecione **Remover administrador** e, em seguida, selecione **Salvar**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nova funcionalidade de administrador do Azure AD para MI

A tabela abaixo resume a funcionalidade para a visualização pública do admin de login do Azure AD para MI, versus uma nova funcionalidade entregue com GA para logins Azure AD.

| Azure AD login admin para MI durante a visualização pública | Funcionalidade GA para administrador Azure AD para MI |
| --- | ---|
| Comporta-se de forma semelhante ao administrador do Azure AD para Banco de Dados SQL, que permite a autenticação do Azure AD, mas o administrador do Azure AD não pode criar logins Azure AD ou SQL no db mestre para MI. | O administrador do Azure AD tem permissão de sysadmin e pode criar logins AAD e SQL no master db para MI. |
| Não está presente na visão sys.server_principals | Está presente na visão sys.server_principals |
| Permite que usuários convidados individuais do Azure AD sejam configurados como administrador do Azure AD para MI. Para obter mais informações, consulte [Adicionar usuários de colaboração B2B do Active Directory no portal Azure](../active-directory/b2b/add-users-administrator.md). | Requer a criação de um grupo Azure AD com usuários convidados como membros para configurar esse grupo como um administrador azure AD para MI. Para obter mais informações, consulte [os negócios do Azure AD para suporte aos negócios](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Como uma prática recomendada para os administradores Azure AD existentes para MI criados antes de GA, e ainda operando o GA, redefinir o administrador do Azure AD usando o portal Azure "Remover administrador" e "Definir admin" opção para o mesmo usuário ou grupo Azure AD.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Problemas conhecidos com o azure AD login GA para MI

- Se houver um login Azure AD no banco de dados mestre para `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`MI, criado usando o comando T-SQL, ele não poderá ser configurado como um administrador azure AD para MI. Você experimentará um erro ao definir o login como um administrador azure AD usando os comandos azure portal, PowerShell ou CLI para criar o login Azure AD.
  - O login deve ser descartado no banco `DROP LOGIN [myaadaccount]`de dados principal usando o comando, antes que a conta possa ser criada como um administrador admin Azure.
  - Configure a conta de administração do Azure AD no `DROP LOGIN` portal Azure após o sucesso. 
  - Se você não puder configurar a conta de administrador do Azure AD, verifique no banco de dados mestre da instância gerenciada para o login. Use o seguinte comando: `SELECT * FROM sys.server_principals`
  - A configuração de um administrador azure AD para MI criará automaticamente um login no banco de dados mestre para esta conta. A remoção do administrador do Azure AD irá automaticamente retirar o login do banco de dados mestre.

- Os usuários convidados individuais do Azure AD não são suportados como administradores do Azure AD para MI. Os usuários convidados devem fazer parte de um grupo Azure AD a ser configurado como administrador do Azure AD. Atualmente, a lâmina do portal Azure não adere aos usuários convidados para outro AD do Azure, permitindo que os usuários continuem com a configuração do administrador. Salvar usuários convidados como administrador do Azure AD fará com que a configuração falhe.
  - Se você deseja fazer de um usuário convidado um administrador Azure AD para MI, inclua o usuário convidado em um grupo Azure AD e defina esse grupo como um administrador do Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell para instância gerenciada sql

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

> [!IMPORTANT]
> O módulo RM (PowerShell Azure Resource Manager, gerente de recursos do Azure) ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará recebendo correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [Introduzindo o novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Para provisionar um administrador do AD do Azure, execute os seguintes comandos do Azure PowerShell:

- Connect-AzAccount
- Assinatura Select-Az

Cmdlets usados para provisionar e gerenciar o administrador do Azure AD para instância gerenciada sql:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Disposições um administrador Azure AD para instância gerenciada SQL na assinatura atual. (Deve ser da assinatura atual)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Remove um administrador Azure AD para instância gerenciada sql na assinatura atual. |
| [Administrador de diretórioativo do Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Retorna informações sobre um administrador azure AD para instância gerenciada sql na assinatura atual.|

O comando a seguir obtém informações sobre um administrador AD do Azure para uma instância gerenciada chamada ManagedInstance01 que está associada a um grupo de recursos chamado ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

As seguintes disposições de comando um grupo de administradores Azure AD chamado DBAs para a instância gerenciada chamada ManagedInstance01. Este servidor está associado ao grupo de recursos ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

O comando a seguir remove o administrador do Azure AD para a instância gerenciada chamada ManagedInstanceName01 associada ao grupo de recursos ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você também pode provisionar um administrador Azure AD para instância gerenciada SQL, chamando os seguintes comandos CLI:

| Comando | Descrição |
| --- | --- |
|[az sql mi ad-admin criar](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Provisões um administrador do Azure Active Directory para instância gerenciada sql. (Deve ser da assinatura atual) |
|[az sql mi ad-admin excluir](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Remove um administrador do Azure Active Directory para a instância gerenciada pelo SQL. |
|[az sql mi lista de ad-admin](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Retorna informações sobre um administrador do Azure Active Directory atualmente configurado para instância gerenciada pelo SQL. |
|[az sql mi ad-admin atualização](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Atualiza o administrador do Active Directory para uma instância gerenciada por SQL. |

Para obter mais informações sobre os comandos CLI, consulte [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Provisionar um administrador do Azure Active Directory para o servidor do Banco de Dados SQL do Azure

> [!IMPORTANT]
> Siga essas etapas apenas se estiver provisionando um servidor de banco de dados SQL Do Azure ou o Azure Synapse Analytics.

Os dois procedimentos a seguir mostram como provisionar um administrador do Azure Active Directory para seu servidor do Azure SQL Server no Portal do Azure e usando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, selecione a conexão para exibir uma lista suspensa dos Active Directories possíveis. Escolha o Active Directory correto como o AD do Azure padrão. Esta etapa vincula o Active Directory associado à assinatura ao Azure SQL Server, verificando se a mesma assinatura é usada tanto para o Azure AD quanto para o SQL Server. (O servidor Azure SQL pode estar hospedando o Banco de Dados SQL do Azure ou o Azure Synapse.)

    ![choose-ad][8]

2. Procure e selecione **o servidor SQL**.

    ![Procure e selecione servidores SQL](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Nessa página, antes você selecionar **SQL servers**, você pode selecionar a **estrela** ao lado do nome para estabelecer a categoria como *favorito* e adicionar **SQL servers**à barra de navegação à esquerda.

3. Na página **SQL Server**, selecione **Administrador do Active Directory**.

4. Na página **Administrador do Active Directory**, selecione **Definir administrador**.

    ![Servidores SQL definem admin active directory](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Na página **Adicionar administrador,** procure um usuário, selecione o usuário ou grupo para ser um administrador e, em seguida, **selecione Selecionar**. A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Usuários ou grupos que estão esmaecidos não podem ser selecionados porque eles não têm suporte como administradores do AD do Azure. (Veja a lista de administradores suportados na seção Recursos e Limitações do **Azure AD da** autenticação do [Azure Active Directory para autenticação com o Banco de Dados SQL ou o Azure Synapse](sql-database-aad-authentication.md).) O RBAC (Role-based Access Control, controle de acesso baseado em função) se aplica apenas ao portal e não é propagado para o SQL Server.

    ![Selecione admin de diretório ativo do Azure](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. Na parte superior da página de **admin Active Directory,** selecione **SAVE**.

    ![salvar administrador](./media/sql-database-aad-authentication/save-admin.png)

O processo de alteração do administrador pode levar vários minutos. O novo administrador aparece na caixa **Administrador do Active Directory** .

   > [!NOTE]
   > Ao configurar o administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode já estar presente no banco de dados mestre virtual como um usuário de autenticação do SQL Server. Se presente, a configuração de administração do AD do Azure falhará, revertendo sua criação e indicando que esse administrador (nome) já existe. Como esse usuário de autenticação do SQL Server não é parte do Azure AD, qualquer esforço para se conectar ao servidor usando a autenticação do Azure AD falhará.

Para remover um Administrador mais tarde, na parte superior da página **Administrador do Active Directory**, selecione **Remover administrador** e, em seguida, selecione **Salvar**.

### <a name="powershell-for-azure-sql-database-and-azure-synapse"></a>PowerShell para banco de dados SQL Azure e Sinapse Azure

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview). Para provisionar um administrador do AD do Azure, execute os seguintes comandos do Azure PowerShell:

- Connect-AzAccount
- Assinatura Select-Az

Cmdlets usados para provisionar e gerenciar a administradora Azure AD para o Banco de Dados SQL Do Azure e o Azure Synapse:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Provisões um administrador do Azure Active Directory para o servidor Azure SQL ou o Azure Synapse. (Deve ser da assinatura atual) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador do Azure Active Directory para o servidor Azure SQL ou o Azure Synapse. |
| [Administrador de diretórioativo do Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retorna informações sobre um administrador do Azure Active Directory atualmente configurado para o servidor Azure SQL ou Azure Synapse. |

Use o comando PowerShell get-help para ver mais informações para cada um desses comandos. Por exemplo, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

O seguinte script provisiona um grupo de administradores do Azure AD nomeado **DBA_Group** (ID de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o servidor **demo_server** em um grupo de recursos nomeado **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure AD ou o Nome UPN. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do AD do Azure, há suporte apenas para o nome de exibição do AD do Azure.

> [!NOTE]
> O comando ```Set-AzSqlServerActiveDirectoryAdministrator``` do Azure PowerShell não impede que você provisione administradores do Azure AD para usuários sem suporte. Um usuário para o qual não há suporte pode ser provisionado, mas não pode se conectar a um banco de dados.

O seguinte exemplo usa a **ObjectID**opcional:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> A **ObjectID** do Azure AD é necessária quando o **DisplayName** não é exclusivo. Para recuperar os valores de **ObjectID** e **DisplayName**, use a seção do Active Directory do Portal Clássico do Azure e exiba as propriedades de um usuário ou grupo.

O exemplo a seguir retorna informações sobre o administrador atual do Azure AD para o Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O seguinte exemplo remove um administrador do AD do Azure:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode provisionar um administrador ad azure chamando os seguintes comandos CLI:

| Comando | Descrição |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Provisões um administrador do Azure Active Directory para o servidor Azure SQL ou o Azure Synapse. (Deve ser da assinatura atual) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Remove um administrador do Azure Active Directory para o servidor Azure SQL ou o Azure Synapse. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Retorna informações sobre um administrador do Azure Active Directory atualmente configurado para o servidor Azure SQL ou Azure Synapse. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Atualiza o administrador do Active Directory para um servidor Azure SQL ou Azure Synapse. |

Para obter mais informações sobre os comandos CLI, consulte [o servidor az sql](/cli/azure/sql/server).

* * *

> [!NOTE]
> Você também pode provisionar um administrador do Azure Active Directory usando as APIs REST. Para obter mais informações, confira [Referência da API REST de Gerenciamento de Serviços e Operações do Banco de Dados SQL do Azure](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Configurar os computadores cliente

Em todas as máquinas clientes, das quais seus aplicativos ou usuários se conectam ao Banco de Dados SQL do Azure ou ao Azure Synapse usando identidades AD do Azure, você deve instalar o seguinte software:

- .NET Framework 4.6 [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)ou posterior a partir de .
- Biblioteca de autenticação de diretório ativo do Azure para sql server (*ADAL. DLL*). Abaixo estão os links de download para instalar o driver SSMS, ODBC e OLE DB mais recente que contém o *ADAL. Biblioteca DLL.*
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC Driver 17 for SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [Driver OLE DB 18 para servidor SQL](https://www.microsoft.com/download/details.aspx?id=56730)

Você pode atender a esses requisitos:

- A instalação da versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [do SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) atende ao requisito do .NET Framework 4.6.
    - O SSMS instala a versão x86 do *ADAL. DLL*.
    - O SSDT instala a versão amd64 do *ADAL. DLL*.
    - O visual studio mais recente do [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende ao requisito .NET Framework 4.6, mas não instala a versão amd64 necessária do *ADAL. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure

> [!IMPORTANT]
> A instância gerenciada agora suporta os princípios (logins) do servidor Azure AD, que permite criar logins de usuários, grupos ou aplicativos do Azure AD. Os princípios do servidor Azure AD (logins) fornecem a capacidade de autenticar à sua instância gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados contido. Para obter mais informações, consulte [visão geral da instância gerenciada](sql-database-managed-instance.md#azure-active-directory-integration). Para obter a sintaxe na criação de entidades de segurança do servidor do Azure AD (logons), consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

A autenticação do Active Directory do Azure exige que os usuários do banco de dados sejam criados como usuários do banco de dados independente. Um usuário de banco de dados independente com base em uma identidade do AD do Azure é um usuário de banco de dados que não tem um logon no banco de dados mestre e que mapeia para uma identidade no diretório do AD do Azure que está associada ao banco de dados. A identidade do AD do Azure pode ser uma conta de usuário individual ou um grupo. Para saber mais sobre usuários de bancos de dados independentes, veja [Usuários do bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Os usuários do banco de dados (com exceção dos administradores) não podem ser criados usando o portal do Azure. As funções RBAC não são propagadas para sql server, sql database ou azure Synapse. As funções RBAC do Azure são usadas para gerenciar Recursos do Azure e não se aplicam às permissões de banco de dados. Por exemplo, a função **SQL Server Contributor** não concede acesso para se conectar ao Banco de Dados SQL ou ao Azure Synapse. A permissão de acesso deve ser concedida diretamente no banco de dados usando instruções Transact-SQL.

> [!WARNING]
> Não há suporte para caracteres especiais como dois-pontos `:` ou E comercial `&`, quando incluídos como nomes de usuário nas instruções T-SQL CREATE LOGIN e CREATE USER.

Para criar um usuário de banco de dados independente baseado no Azure AD (que não seja o administrador do servidor proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD como um usuário com, no mínimo, a permissão **ALTER ANY USER** . Em seguida, use a sintaxe Transact-SQL a seguir:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*nome_UPN_Azure_AD* pode ser o nome UPN de um usuário do Azure AD ou o nome de exibição de um grupo do Azure AD.

**Exemplos:** para criar um usuário de banco de dados independente que representa um usuário de domínio federado ou gerenciado pelo Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente que represente um grupo de domínio do AD do Azure AD ou federado, forneça o nome para exibição de um grupo de segurança:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente que representa um aplicativo que se conectará usando um token do Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Este comando exige que o SQL acesse o Azure AD (o "provedor externo") em nome do usuário logado. Às vezes, surgirão circunstâncias que fazem com que o Azure AD retorne uma exceção ao SQL. Nestes casos, o usuário verá o erro SQL 33134, que deve conter a mensagem de erro específica do AAD. Na maioria das vezes, o erro dirá que o acesso é negado, ou que o usuário deve se inscrever no MFA para acessar o recurso, ou que o acesso entre aplicativos de primeira parte deve ser tratado via pré-autorização. Nos dois primeiros casos, o problema geralmente é causado por políticas de Acesso Condicional que são definidas no inquilino AAD do usuário: impedem que o usuário acesse o provedor externo. A atualização das políticas de CA para permitir o acesso ao aplicativo '00000002-0000-0000-0000000000000000' (o ID do aplicativo AAD Graph API) deve resolver o problema. No caso de o erro dizer que o acesso entre aplicativos de primeira parte deve ser tratado via pré-autorização, o problema é porque o usuário está conectado como um diretor de serviço. O comando deve ser bem sucedido se for executado por um usuário.

> [!TIP]
> Não é possível criar um usuário diretamente de um Azure Active Directory que não seja o Azure Active Directory associado à sua assinatura do Azure. No entanto, os membros de outros Active Directories que são os usuários importados no Active Directory associado (conhecidos como usuários externos) podem ser adicionados a um grupo do Active Directory no Active Directory locatário. Ao criar um usuário de banco de dados independente para esse grupo AD, os usuários do Active Directory externo podem obter acesso ao Banco de Dados SQL.

Para obter mais informações sobre como criar usuários de banco de dados independente baseados em identidades do Active Directory do Azure, consulte [CRIAR USUÁRIO (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Removendo o administrador do Azure Active Directory para o Azure SQL Server impede que qualquer usuário da autenticação do Azure AD se conecte ao servidor. Se for necessário, os usuários do Azure AD inutilizáveis poderão ser removidos manualmente por um administrador do Banco de Dados SQL.

> [!NOTE]
> Se você receber um **Tempo Limite de Conexão Expirado**, talvez seja necessário definir o parâmetro `TransparentNetworkIPResolution` da cadeia de conexão como falso. Para obter mais informações, consulte [Problema de tempo limite de conexão com o .NET Framework 4.6.1 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Quando você cria um usuário de banco de dados, o usuário recebe a permissão **CONNECT** e pode se conectar a esse banco de dados como membro da função **PUBLIC**. Inicialmente, as únicas permissões disponíveis para o usuário são as permissões concedidas à função **PUBLIC** ou as permissões concedidas a quaisquer grupos do Azure AD dos quais esse usuário é membro. A partir do momento que você provisionar um usuário de banco de dados independente baseado no AD do Azure, você pode conceder ao usuário permissões adicionais, do mesmo modo que você concede permissões para qualquer outro tipo de usuário. Normalmente, conceda permissões para funções de banco de dados e adicione usuários a funções. Para saber mais, confira [Noções básicas sobre permissões do Mecanismo de Banco de Dados](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções especiais do Banco de dados SQL, veja [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).
Uma conta de usuário de domínio federado que é importado para um domínio gerenciado como um usuário externo deve usar a identidade do domínio gerenciado.

> [!NOTE]
> Usuários do AD do Azure são marcados nos metadados do banco de dados com tipo E (EXTERNAL_USER) e para grupos com o tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-azure-synapse-by-using-ssms-or-ssdt"></a>Conecte-se ao banco de dados do usuário ou ao Azure Synapse usando SSMS ou SSDT  

Para confirmar que o administrador do Azure AD está configurado corretamente, conecte-se ao banco de dados **mestre** usando a conta de administrador do Azure AD.
Para provisionar um usuário de banco de dados independente com base no Azure AD (que não seja o administrador do servidor que é o proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD que tenha acesso ao banco de dados.

> [!IMPORTANT]
> O suporte à autenticação do Azure Active Directory está disponível com o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e o [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. A versão de agosto de 2016 do SSMS também inclui suporte para Universal autenticação do Active Directory, que permite aos administradores exigir Autenticação Multifator utilizando uma chamada telefônica, mensagem de texto, os cartões inteligentes com PIN ou a notificação de aplicativo móvel.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Usando uma identidade do Azure AD para se conectar usando SSMS ou SSDT

Os procedimentos a seguir mostram como se conectar a um Banco de Dados SQL com uma identidade do Azure AD usando o SQL Server Management Studio ou as Ferramentas de Banco de Dados do SQL Server. 

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Use este método se você estiver conectado ao Windows usando suas credenciais do Azure Active Directory a partir de um domínio federado ou um domínio gerenciado configurado para logon único perfeito para autenticação de hash de passagem e senha. Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](../active-directory/hybrid/how-to-connect-sso.md).

1. Iniciar o Management Studio ou Ferramentas de Dados e na caixa de diálogo **Conectar ao servidor** (ou conectar ao mecanismo de banco de **dados),** na caixa **de autenticação,** selecione **Azure Active Directory - Integrado**. Nenhuma senha é necessária ou pode ser inserida porque suas credenciais existentes serão apresentadas para a conexão.

    ![Selecione Autenticação Integrada do AD][11]

2. Selecione o botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. Para obter mais informações, consulte o artigo [Multi-factor AAD auth](sql-database-ssms-mfa-authentication.md#azure-ad-domain-name-or-tenant-id-parameter) sobre as diferenças entre as Propriedades de Conexão para SSMS 17.x e 18.x. 

    ![Selecione o nome do banco de dados][13]

### <a name="active-directory-password-authentication"></a>Autenticação de senha do Active Directory

Use esse método ao se conectar com um nome de entidade do AD do Azure usando o domínio gerenciado pelo Azure AD. Você também pode usá-lo para contas federadas sem acesso ao domínio, por exemplo, quando trabalhar remotamente.

Use este método para autenticar para SQL DB ou MI com usuários de identidade somente na nuvem do Azure AD, ou aqueles que usam identidades híbridas Azure AD. Este método suporta usuários que desejam usar sua credencial do Windows, mas sua máquina local não está associada ao domínio (por exemplo, usando acesso remoto). Neste caso, um usuário do Windows pode indicar sua conta de domínio e senha, e pode autenticar para SQL DB, MI ou Azure Synapse.

1. Iniciar o Management Studio ou Ferramentas de Dados e na caixa de diálogo **Conectar ao servidor** (ou conectar ao mecanismo de banco de **dados),** na caixa **de autenticação,** selecione **Azure Active Directory - Password**.

2. Na caixa Nome de **usuário,** digite o nome de usuário do Azure Active Directory no formato **nome de usuário\@domain.com**. Os nomes de usuário devem ser uma conta do Azure Active Directory ou uma conta de um domínio gerenciado ou federado com o Azure Active Directory.

3. Na caixa **Senha,** digite sua senha de usuário para a conta do Azure Active Directory ou conta de domínio gerenciada/federada.

    ![Selecione Autenticação de Senha do AD][12]

4. Selecione o botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. (Confira o gráfico na opção anterior.)

### <a name="active-directory-interactive-authentication"></a>Autenticação interativa do Active Directory

Use este método para autenticação interativa com ou sem autenticação multifatorial (MFA), com senha sendo solicitada de forma interativa. Este método pode ser usado para autenticar para SQL DB, MI e Azure Synapse para usuários de identidade somente em nuvem do Azure AD, ou aqueles que usam identidades híbridas Azure AD.

Para obter mais informações, consulte [Usando autenticação AAD multifatorial com banco de dados SQL Azure e Azure Synapse Analytics (suporte a SSMS para MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade do Azure AD para conectar-se de um aplicativo cliente

Os procedimentos a seguir mostram como se conectar a um Banco de Dados SQL com uma identidade do Azure AD de um aplicativo cliente.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Para usar a autenticação integrada do Windows, o Active Directory do seu domínio deve ser federado com o Azure Active Directory ou deve ser um domínio gerenciado configurado para login único perfeito para autenticação de hash de passagem ou senha. Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) para a autenticação integrada do Windows não é compatível com o logon único contínuo para autenticação de passagem e de hash de senha.

Seu aplicativo cliente (ou um serviço) se conectando ao banco de dados deve estar sendo executado em uma máquina de domínio situada sob as credenciais de domínio de um usuário.

Para se conectar a um banco de dados usando autenticação integrada e uma identidade Azure `Active Directory Integrated`AD, a palavra-chave Autenticação na seqüência de conexões do banco de dados deve ser definida como . O exemplo de código em C# a seguir usa ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Para a conexão ao Banco de Dados SQL do Azure, não há suporte para a palavra-chave de cadeia de conexão `Integrated Security=True`. Ao realizar uma conexão ODBC, você precisará remover espaços e configurar a autenticação para 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticação de senha do Active Directory

Para se conectar a um banco de dados usando contas de usuário de identidade somente em nuvem do Azure `Active Directory Password`AD ou aqueles que usam identidades híbridas Azure AD, a palavra-chave Autenticação deve ser definida como . A cadeia de conexão deve conter valores e palavras-chave de ID/UID de Usuário e Senha/PWD. O exemplo de código em C# a seguir usa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre métodos de autenticação do Azure AD usando os exemplos de código de demonstração disponíveis em [Demonstração do GitHub de Autenticação do Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token do Azure AD

Este método de autenticação permite que serviços intermediários obtenham [JSON Web Tokens (JWT)](../active-directory/develop/id-tokens.md) para se conectar ao Azure SQL Database ou ao Azure Synapse, obtendo um token do Azure Active Directory (AAD). Esse método permite vários cenários de aplicativos, incluindo identidades de serviço, diretores de serviço e aplicativos usando autenticação baseada em certificados. Você precisa concluir quatro etapas básicas para usar a autenticação de token do Azure AD:

1. Registre seu aplicativo no Azure Active Directory e obtenha o ID do cliente para o seu código.
2. Criar um usuário de banco de dados que representa o aplicativo. (Concluída anteriormente na etapa 6).
3. Crie um certificado no computador cliente que executa o aplicativo.
4. Adicionar o certificado como uma chave para seu aplicativo.

Exemplo de cadeia de conexão:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, confira [Blog de segurança do SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obter informações sobre como adicionar um certificado, consulte [Introdução à autenticação baseada em certificado no Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As instruções a seguir, conexão usando a versão 13.1 do sqlcmd, que está disponível no [Centro de Download](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd`com `-G` o comando não funciona com identidades do sistema e requer um login principal do usuário.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Solução de problemas autenticação Ad do Azure

Orientações sobre problemas de solução de problemas com a autenticação ad do Azure podem ser encontradas no blog a seguir:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos logins, usuários, funções de banco de dados e permissões no Banco de Dados SQL, consulte [Logins, usuários, funções de banco](sql-database-manage-logins.md)de dados e contas de usuário .
- Para obter mais informações sobre objetos de banco de dados, confira [Entidades](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de banco de dados, confira [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121.aspx).
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
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
