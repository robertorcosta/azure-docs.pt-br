---
title: Acesso condicional
description: Saiba como configurar o acesso condicional para o banco de dados SQL do Azure e o Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124900"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Acesso condicional (MFA) com o banco de dados SQL do Azure e o Azure Synapse Analytics

O [banco de dados SQL](sql-database-technical-overview.md)do Azure, o [instância gerenciada](sql-database-managed-instance.md)e o [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) dão suporte ao acesso condicional da Microsoft. 

> [!NOTE]
> Este tópico aplica-se ao SQL Server do Azure e ao banco de dados SQL e ao Azure Synapse que são criados no SQL Server do Azure. Para simplificar, o banco de dados SQL é usado ao fazer referência ao banco de dados SQL e ao Azure Synapse.

As próximas etapas mostram como configurar o Banco de Dados SQL para impor uma política de Acesso Condicional.  

## <a name="prerequisites"></a>Pré-requisitos  
- Você deve configurar o banco de dados SQL ou o pool SQL no Azure Synapse para dar suporte à autenticação Azure Active Directory. Para obter etapas específicas, consulte [configurar e gerenciar a autenticação de Azure Active Directory com o banco de dados SQL ou o Azure Synapse](sql-database-aad-authentication-configure.md).  
- Quando a autenticação multifator estiver habilitada, você deverá se conectar com uma ferramenta com suporte, como o último SSMS. Para obter mais informações, consulte [Configurar a autenticação multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurar a AC para o BD SQL do Azure/DW  
1. Entre no portal, selecione **Azure Active Directory**e, em seguida, selecione **acesso condicional**. Para obter mais informações, consulte [Referência técnica do Acesso Condicional ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Folha acesso condicional](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Na folha **Políticas de Acesso Condicional**, clique em **Nova política**, forneça um nome e, em seguida, clique em **Configurar regras**.  
3. Em **atribuições**, selecione **usuários e grupos**, marque **Selecionar usuários e grupos**e, em seguida, selecione o usuário ou grupo para acesso condicional. Clique em **Selecionar** e, em seguida, em **Concluído** para aceitar a seleção.  
   ![selecionar usuários e grupos](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selecione **Aplicativos na nuvem** e clique em **Selecionar aplicativos**. Você verá todos os aplicativos disponíveis para acesso condicional. Selecione **Banco de Dados SQL do Azure**, na parte inferior clique em **Selecionar** e, em seguida, clique em **Concluído**.  
   ![selecionar Banco de Dados SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Se você não encontrar o **banco de dados SQL do Azure** listado na terceira captura de tela a seguir, conclua as seguintes etapas:   
   - Entre na instância do BD SQL do Azure/DW usando o SSMS com uma conta do administrador do AAD.  
   - Execute `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Entre no AAD e verifique se o banco de dados SQL do Azure e o Azure Synapse estão listados nos aplicativos em seu AAD.  

5. Selecione **Controles de acesso**, selecione **Conceder** e, em seguida, marque a política que você deseja aplicar. Para este exemplo, selecionamos **Exigir autenticação multifator**.  
   ![selecionar conceder acesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Resumo  
O aplicativo selecionado (Banco de Dados SQL do Azure), que permite se conectar ao BD SQL do Azure/DW usando o Azure AD Premium, agora impõe a política de Acesso Condicional selecionada, **Exigir autenticação multifator.**  
Para perguntas sobre o banco de dados SQL do Azure e o Azure Synapse sobre a MFAforSQLDB@microsoft.comautenticação multifator, entre em contato com.  

## <a name="next-steps"></a>Próximas etapas  

Para obter um tutorial, consulte [Proteger o Banco de Dados SQL do Azure](sql-database-security-tutorial.md).
