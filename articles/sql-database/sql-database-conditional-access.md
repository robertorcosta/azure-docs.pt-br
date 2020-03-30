---
title: Acesso Condicional
description: Saiba como configurar o Conditional Access para o Banco de Dados SQL do Azure e o Azure Synapse.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124900"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Acesso Condicional (MFA) com banco de dados SQL Azure e Análise sinapse do Azure

O Banco de [Dados SQL](sql-database-technical-overview.md)do Azure, [a instância gerenciada](sql-database-managed-instance.md)e [o Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) suportam o Microsoft Conditional Access. 

> [!NOTE]
> Este tópico se aplica ao servidor Azure SQL e ao SQL Database e ao Azure Synapse que são criados no servidor SQL do Azure. Para simplificar, o Banco de Dados SQL é usado quando se refere tanto ao Banco de Dados SQL quanto ao Sinapse Azure.

As próximas etapas mostram como configurar o Banco de Dados SQL para impor uma política de Acesso Condicional.  

## <a name="prerequisites"></a>Pré-requisitos  
- Você deve configurar seu pool SQL Database ou SQL no Azure Synapse para suportar a autenticação do Azure Active Directory. Para etapas específicas, consulte [Configurar e gerenciar a autenticação do Azure Active Directory com o SQL Database ou o Azure Synapse](sql-database-aad-authentication-configure.md).  
- Quando a autenticação multifator estiver habilitada, você deverá se conectar com uma ferramenta com suporte, como o último SSMS. Para obter mais informações, consulte [Configurar a autenticação multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurar a AC para o BD SQL do Azure/DW  
1. Faça login no Portal, selecione **O Diretório Ativo do Azure**e selecione Acesso **Condicional**. Para obter mais informações, consulte [Referência técnica do Acesso Condicional ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Lâmina de acesso condicional](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Na folha **Políticas de Acesso Condicional**, clique em **Nova política**, forneça um nome e, em seguida, clique em **Configurar regras**.  
3. Em **Atribuições,** selecione **Usuários e grupos,** verifique Selecionar usuários e **grupos**e, em seguida, selecione o usuário ou grupo para acesso condicional. Clique em **Selecionar** e, em seguida, em **Concluído** para aceitar a seleção.  
   ![selecionar usuários e grupos](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selecione **Aplicativos na nuvem** e clique em **Selecionar aplicativos**. Você vê todos os aplicativos disponíveis para acesso condicional. Selecione **Banco de Dados SQL do Azure**, na parte inferior clique em **Selecionar** e, em seguida, clique em **Concluído**.  
   ![selecionar Banco de Dados SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Se você não conseguir encontrar **o Azure SQL Database** listado na terceira captura de tela a seguir, complete as seguintes etapas:   
   - Entre na instância do BD SQL do Azure/DW usando o SSMS com uma conta do administrador do AAD.  
   - Execute `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Faça login no AAD e verifique se o Banco de Dados SQL do Azure e o Azure Synapse estão listados nos aplicativos em seu AAD.  

5. Selecione **Controles de acesso**, selecione **Conceder** e, em seguida, marque a política que você deseja aplicar. Para este exemplo, selecionamos **Exigir autenticação multifator**.  
   ![selecionar conceder acesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Resumo  
O aplicativo selecionado (Banco de Dados SQL do Azure), que permite se conectar ao BD SQL do Azure/DW usando o Azure AD Premium, agora impõe a política de Acesso Condicional selecionada, **Exigir autenticação multifator.**  
Para dúvidas sobre o Banco de Dados Azure SQL e o Azure Synapse sobre autenticação multifatorial, entre em contato MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Próximas etapas  

Para obter um tutorial, consulte [Proteger o Banco de Dados SQL do Azure](sql-database-security-tutorial.md).
