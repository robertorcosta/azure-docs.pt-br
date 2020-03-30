---
title: Habilite o AAD para o tempo de execução da integração do Azure SSIS
description: Este artigo descreve como habilitar a autenticação do Microsoft Azure Active Directory com a identidade gerenciada do Azure Data Factory para criar o Azure-SSIS Integration Runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 70367a38fbf7b59486e2eaaf6c05634aa7575869
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260703"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Habilitar a autenticação do Azure Active Directory para o Azure-SSIS Integration Runtime

Este artigo mostra como habilitar a autenticação do Azure Active Directory (Azure AD) com a identidade gerenciada para a Azure Data Factory (ADF) e usá-la em vez de métodos convencionais de autenticação (como autenticação SQL) para:

- Crie um Ir (Azure-SSIS Integration Runtime, execução de integração do Azure-SSIS) que, por sua vez, provisionará o banco de dados de catálogo SSIS (SSISDB) no servidor/instância gerenciada do Banco de Dados Azure SQL em seu nome.

- Conecte-se a vários recursos do Azure ao executar pacotes SSIS no Azure-SSIS IR.

Para obter mais informações sobre a identidade gerenciada para o seu ADF, consulte [Identificador gerenciado para Fábrica de Dados](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  Neste cenário, a autenticação do Azure AD com a identidade gerenciada para o seu ADF só é usada na criação e subsequentes operações iniciais do seu SSIS IR que, por sua vez, provisão e se conectará ao SSISDB. Para execuções de pacoteS SSIS, seu SSIS IR ainda se conectará ao SSISDB usando autenticação SQL com contas totalmente gerenciadas que são criadas durante o provisionamento do SSISDB.
>-  Se você já criou seu SSIS IR usando autenticação SQL, você não pode reconfigurá-lo para usar a autenticação Azure AD via PowerShell neste momento, mas você pode fazê-lo através do aplicativo Azure portal/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Habilitar o Azure AD no Banco de Dados SQL do Azure

O servidor do Banco de Dados SQL do Azure oferece suporte à criação de um banco de dados com um usuário do Azure AD. Primeiro, é necessário criar um grupo do Azure AD com a identidade gerenciada para o ADF como um membro. Em seguida, você precisa definir um usuário do Azure AD como o administrador do Active Directory para o servidor do Banco de Dados SQL do Azure e, então, conectar a ele no SSMS (SQL Server Management Studio) usando esse usuário. Por fim, será necessário criar um usuário independente que representa o grupo do Azure AD, de modo que a identidade gerenciada para o ADF possa ser usada pelo Azure-SSIS IR para criar o SSISDB em seu nome.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Criar um grupo do Azure AD com a identidade gerenciada para o ADF como um membro

É possível usar um grupo do Azure AD existente ou criar um novo usando o PowerShell do Azure AD.

1.  Instale o módulo [Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)

2.  Entre usando  `Connect-AzureAD` e execute o cmdlet a seguir para criar um grupo e salvá-lo em uma variável:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    O resultado é semelhante ao exemplo a seguir, que também exibe o valor da variável:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Adicione a identidade gerenciada para o seu ADF ao grupo. Você pode seguir o artigo [Identificado pela Data Factory para](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) obter o ID principal do objeto de identidade gerenciada (por exemplo, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, mas não use ID de aplicativo de identidade gerenciada para este fim).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Também é possível examinar a associação do grupo posteriormente.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Configurar a autenticação do Azure AD para o servidor do Banco de Dados SQL do Azure

É possível  [Configurar e gerenciar a autenticação do Azure AD com o SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) seguindo estas etapas:

1.  No portal Azure, selecione **Todos os serviços** -> **servidores SQL** da navegação à esquerda.

2.  Selecione o servidor do Banco de Dados SQL do Azure a ser configurado com autenticação do Azure AD.

3.  Na seção **Configurações** da folha, selecione **Administrador do Active Directory**.

4.  Na barra de comandos, selecione **Definir administrador**.

5.  Selecione uma conta de usuário Azure AD para ser feita administradora do servidor e, em seguida, **selecione Selecionar.**

6.  Na barra de comando, selecione **Salvar.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Criar um usuário independente no servidor do Banco de Dados SQL do Azure que representa o grupo do Azure AD

Para este próximo passo, você precisa [do Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Inicie o SSMS.

2. Na caixa de diálogo **Conectar ao servidor,** digite o nome do servidor do banco de dados Azure SQL no campo **nome do servidor.**

3. No campo **Autenticação,** selecione **Active Directory - Universal com suporte a MFA** (você também pode usar os outros dois tipos de autenticação do Active Directory, consulte [Configurar e gerenciar a autenticação Azure AD com SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. No campo Nome do **Usuário,** digite o nome da conta Azure AD testuser@xxxonline.comque você define como administrador do servidor, por exemplo.

5. selecione **Conectar** e concluir o processo de login.

6. No **Pesquisador de Objetos**, expanda a pasta **Bancos de dados** -> **Bancos de Dados do Sistema**.

7. Clique com o botão direito do mouse no banco de dados **mestre** e selecione **Nova consulta**.

8. Na janela consulta, digite o seguinte comando T-SQL e selecione **Execute** na barra de ferramentas.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   O comando deve ser concluído com êxito, criando um usuário independente para representar o grupo.

9. Limpe a janela de consulta, insira a o comando T-SQL a seguir e selecione **Executar** na barra de ferramentas.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   O comando deve ser concluído com êxito, concedendo ao usuário independente a capacidade de criar um banco de dados (SSISDB).

10. Se o seu SSISDB foi criado usando a autenticação SQL e você quiser alternar para usar a autenticação Azure AD para o seu Azure-SSIS IR para acessá-lo, clique com o botão direito do mouse no banco de dados **SSISDB** e selecione **Nova consulta**.

11. Na janela consulta, digite o seguinte comando T-SQL e selecione **Execute** na barra de ferramentas.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    O comando deve ser concluído com êxito, criando um usuário independente para representar o grupo.

12. Limpe a janela de consulta, insira a o comando T-SQL a seguir e selecione **Executar** na barra de ferramentas.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    O comando deve ser concluído com êxito, concedendo ao usuário independente a capacidade de acessar o SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Habilitar o Azure AD na Instância Gerenciada do Banco de Dados SQL do Azure

A Instância Gerenciada do Banco de Dados SQL do Azure dá suporte à criação de um banco de dados com a identidade gerenciada diretamente para o ADF. Não é necessário unir a identidade gerenciada para o ADF para um grupo do Azure AD nem criar um usuário independente que represente esse grupo em sua Instância Gerenciada.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Configurar a autenticação do Azure AD para a Instância Gerenciada do Banco de Dados SQL do Azure

Siga as etapas em [Provision um administrador do Diretório Ativo do Azure para sua instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Adicionar a identidade gerenciada para o ADF como um usuário na Instância Gerenciada do Banco de Dados SQL do Azure

Para este próximo passo, você precisa [do Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Inicie o SSMS.

2.  Conecte-se à instância gerenciada usando uma conta sql server que é um **sysadmin**. Esta é uma limitação temporária que será removida assim que os princípios do servidor Azure AD (logins) para a instância gerenciada do banco de dados SQL do Azure se tornarem GA. Você verá o seguinte erro se tentar usar uma conta administrativa azure AD para criar o login: Msg 15247, Nível 16, Estado 1, O Usuário da Linha 1 não tem permissão para executar essa ação.

3.  No **Pesquisador de Objetos**, expanda a pasta **Bancos de dados** -> **Bancos de Dados do Sistema**.

4.  Clique com o botão direito do mouse no banco de dados **mestre** e selecione **Nova consulta**.

5.  Na janela de consulta, execute o seguinte script T-SQL para adicionar a identidade gerenciada para seu ADF como usuário

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    O comando deve ser concluído com êxito, concedendo à identidade gerenciada para o ADF a capacidade de criar um banco de dados (SSISDB).

6.  Se o seu SSISDB foi criado usando a autenticação SQL e você quiser alternar para usar a autenticação Azure AD para o seu Azure-SSIS IR para acessá-lo, clique com o botão direito do mouse no banco de dados **SSISDB** e selecione **Nova consulta**.

7.  Na janela consulta, digite o seguinte comando T-SQL e selecione **Execute** na barra de ferramentas.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    O comando deve ser concluído com êxito, concedendo à identidade gerenciada para o ADF a capacidade de acessar o SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Provisionar o Azure-SSIS IR no portal do Azure/aplicativo ADF

Ao provisionar o Azure-SSIS IR no portal do Azure/aplicativo ADF, na página **Configurações do SQL**, selecione a opção **Usar autenticação do AAD com a identidade gerenciada para o ADF**. A captura de tela a seguir mostra as configurações do IR com o servidor do Banco de Dados SQL do Azure hospedando SSISDB. Para IR com Instância Gerenciada hospedando SSISDB, as configurações **Camada de Serviço Catálogo do Banco de Dados** e **Permitir acesso dos serviços do Azure** não são aplicáveis, enquanto que as outras configurações são os mesmas.

Para obter mais informações sobre como criar um Azure-SSIS IR, consulte [Criar um runtime de integração do Azure-SSIS no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Configurações do runtime de integração do Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Provisionar o Azure-SSIS IR com o PowerShell

Para provisionar seu IR do Azure-SSIS com o PowerShell, faça o seguinte:

1.  Instale o módulo [Azure PowerShell.](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 

2.  No seu script, não defina o parâmetro `CatalogAdminCredential`. Por exemplo: 

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Execute pacotes SSIS com autenticação de identidade gerenciada

Quando você executa pacotes SSIS no Azure-SSIS IR, você pode usar autenticação de identidade gerenciada para se conectar a vários recursos do Azure. Atualmente, já suportamos autenticação de identidade gerenciada nos seguintes gerentes de conexão.

- [gerenciador de conexões OLE DB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gerenciador de conexões ADO.NET](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gerenciador de conexão de armazenamento azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
