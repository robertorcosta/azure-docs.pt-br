---
title: Enable Azure Active Directory for Azure-SSIS Integration Runtime
description: Este artigo descreve como habilitar a autenticação do Microsoft Azure Active Directory com a identidade gerenciada do Azure Data Factory para criar o Azure-SSIS Integration Runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.date: 5/14/2019
ms.openlocfilehash: 6973e72b06d51241e883038936270fd0931365d7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217705"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Habilitar a autenticação do Azure Active Directory para o Azure-SSIS Integration Runtime

This article shows you how to enable Azure Active Directory (Azure AD) authentication with the managed identity for your Azure Data Factory (ADF) and use it instead of conventional authentication methods (like SQL authentication) to:

- Create an Azure-SSIS Integration Runtime (IR) that will in turn provision SSIS catalog database (SSISDB) in Azure SQL Database server/Managed Instance on your behalf.

- Connect to various Azure resources when running SSIS packages on Azure-SSIS IR.

For more info about the managed identity for your ADF, see [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  In this scenario, Azure AD authentication with the managed identity for your ADF is only used in the creation and subsequent starting operations of your SSIS IR that will in turn provision and connect to SSISDB. For SSIS package executions, your SSIS IR will still connect to SSISDB using SQL authentication with fully managed accounts that are created during SSISDB provisioning.
>-  If you have already created your SSIS IR using SQL authentication, you can not reconfigure it to use Azure AD authentication via PowerShell at this time, but you can do so via Azure portal/ADF app. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Habilitar o Azure AD no Banco de Dados SQL do Azure

O servidor do Banco de Dados SQL do Azure oferece suporte à criação de um banco de dados com um usuário do Azure AD. Primeiro, é necessário criar um grupo do Azure AD com a identidade gerenciada para o ADF como um membro. Em seguida, você precisa definir um usuário do Azure AD como o administrador do Active Directory para o servidor do Banco de Dados SQL do Azure e, então, conectar a ele no SSMS (SQL Server Management Studio) usando esse usuário. Por fim, será necessário criar um usuário independente que representa o grupo do Azure AD, de modo que a identidade gerenciada para o ADF possa ser usada pelo Azure-SSIS IR para criar o SSISDB em seu nome.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Criar um grupo do Azure AD com a identidade gerenciada para o ADF como um membro

É possível usar um grupo do Azure AD existente ou criar um novo usando o PowerShell do Azure AD.

1.  Instale o módulo do [PowerShell do Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

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

3.  Adicione a identidade gerenciada para o seu ADF ao grupo. You can follow the article [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) to get the principal Managed Identity Object ID (e.g. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, but do not use Managed Identity Application ID for this purpose).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Também é possível examinar a associação do grupo posteriormente.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Configurar a autenticação do Azure AD para o servidor do Banco de Dados SQL do Azure

É possível  [Configurar e gerenciar a autenticação do Azure AD com o SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) seguindo estas etapas:

1.  No portal do Azure, selecione **Todos os serviços** -> **Servidores SQL** na barra de navegação esquerda.

2.  Selecione o servidor do Banco de Dados SQL do Azure a ser configurado com autenticação do Azure AD.

3.  Na seção **Configurações** da folha, selecione **Administrador do Active Directory**.

4.  Na barra de comandos, selecione **Definir administrador**.

5.  Select an Azure AD user account to be made administrator of the server, and then select **Select.**

6.  Na barra de comandos, selecione **Salvar.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Criar um usuário independente no servidor do Banco de Dados SQL do Azure que representa o grupo do Azure AD

Para esta próxima etapa, você precisará [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Inicie o SSMS.

2. In the **Connect to Server** dialog, enter your Azure SQL Database server name in the **Server name** field.

3. In the **Authentication** field, select **Active Directory - Universal with MFA support** (you can also use the other two Active Directory authentication types, see [Configure and manage Azure AD authentication with SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. In the **User name** field, enter the name of Azure AD account that you set as the server administrator, e.g. testuser@xxxonline.com.

5. select **Connect** and complete the sign-in process.

6. No **Pesquisador de Objetos**, expanda a pasta **Bancos de dados** -> **Bancos de Dados do Sistema**.

7. Clique com o botão direito do mouse no banco de dados **mestre** e selecione **Nova consulta**.

8. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   O comando deve ser concluído com êxito, criando um usuário independente para representar o grupo.

9. Limpe a janela de consulta, insira a o comando T-SQL a seguir e selecione **Executar** na barra de ferramentas.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   O comando deve ser concluído com êxito, concedendo ao usuário independente a capacidade de criar um banco de dados (SSISDB).

10. If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

11. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

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

Follow the steps in [Provision an Azure Active Directory administrator for your Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Adicionar a identidade gerenciada para o ADF como um usuário na Instância Gerenciada do Banco de Dados SQL do Azure

Para esta próxima etapa, você precisará [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Inicie o SSMS.

2.  Connect to your Managed Instance using a SQL Server account that is a **sysadmin**. This is a temporary limitation that will be removed once Azure AD server principals (logins) for Azure SQL Database Managed Instance becomes GA. You will see the following error if you try to use an Azure AD admin account to create the login: Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.

3.  No **Pesquisador de Objetos**, expanda a pasta **Bancos de dados** -> **Bancos de Dados do Sistema**.

4.  Clique com o botão direito do mouse no banco de dados **mestre** e selecione **Nova consulta**.

5.  In the query window, execute the following T-SQL script to add the managed identity for your ADF as a user

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    O comando deve ser concluído com êxito, concedendo à identidade gerenciada para o ADF a capacidade de criar um banco de dados (SSISDB).

6.  If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

7.  In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

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

1.  Instale o módulo[Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

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

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Run SSIS Packages with Managed Identity Authentication

When you run SSIS packages on Azure-SSIS IR, you can use managed identity authentication to connect to various Azure resources. Currently we have already supported managed identity authentication in the following connection managers.

- [OLE DB Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
