---
title: Configurar autenticação multifator
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Saiba como usar a autenticação multifator com o SSMS para o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: b1dbd66e34790599020233c5b1249593a4c0472d
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442642"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configurar a autenticação multifator para SQL Server Management Studio e Azure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo mostra como usar a autenticação multifator (MFA) do Azure Active Directory (Azure AD) com o SQL Server Management Studio (SSMS). O Azure AD MFA pode ser usado ao conectar o SSMS ou o SqlPackage.exe ao [banco de dados SQL do](sql-database-paas-overview.md)Azure, [Azure SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) e [azure Synapse Analytics (anteriormente SQL data warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral da autenticação multifator, consulte [autenticação universal com o banco de dados SQL, sql instância gerenciada e Azure Synapse (suporte do SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> O banco de dados do Azure SQL, o Azure SQL Instância Gerenciada e o Azure Synapse (anteriormente SQL Data Warehouse) são mencionados coletivamente no restante deste artigo como bancos de dados, e o servidor está se referindo ao [servidor](logical-servers.md) que hospeda os bancos de dados do Azure SQL Database e do Synapse.

## <a name="configuration-steps"></a>Etapas de configuração

1. **Configurar um Azure Active Directory** - para saber mais, confira [Administração do seu diretório do Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Integração de suas identidades locais com o Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Adicionar seu próprio nome de domínio ao Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) e [Gerenciar o Azure AD usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurar o MFA** – para obter instruções passo a passo, veja [O que é a Autenticação Multifator do Azure?](../../active-directory/authentication/multi-factor-authentication.md), [Acesso Condicional (MFA) com o Banco de Dados SQL do Azure e o Data Warehouse](conditional-access-configure.md). (O acesso condicional completo requer um Azure Active Directory Premium. MFA limitado está disponível com um Azure AD padrão).
3. **Configurar a autenticação do Azure ad** -para obter instruções passo a passo, consulte [conectando ao banco de dados sql, SQL instância gerenciada ou Azure Synapse usando a autenticação Azure Active Directory](authentication-aad-overview.md).
4. **Baixar o SSMS** - no computador cliente, baixe o SSMS mais recente de [Baixar o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conectando-se usando a autenticação universal com o SSMS

As etapas a seguir mostram como se conectar usando o SSMS mais recente.

1. Para se conectar usando a autenticação universal, na caixa de diálogo **conectar ao servidor** no SQL Server Management Studio (SSMS), selecione **Active Directory-universal com suporte a MFA**. (Se você vir **Autenticação Universal do Active Directory**, não está na versão mais recente do SSMS).

   ![1mfa-universal-connect](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Preencha a caixa **Nome de usuário** com as credenciais do Azure Active Directory, no formato `user_name@domain.com`.

   ![1mfa-universal-connect-user](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Se você estiver se conectando como um usuário convidado, não precisará mais concluir o campo nome de domínio do AD ou ID de locatário para usuários convidados, pois o SSMS 18. x ou posterior o reconhece automaticamente. Para obter mais informações, consulte [autenticação universal com o banco de dados SQL, sql instância gerenciada e Azure Synapse (suporte do SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).

   ![MFA-no-Tenant-SSMS](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   No entanto, se você estiver se conectando como um usuário convidado usando o SSMS 17. x ou mais antigo, você deve clicar em **Opções**e na caixa de diálogo **propriedade de conexão** e concluir a caixa nome de **domínio do AD ou ID de locatário** .

   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Selecione **Opções** e especifique o banco de dados na caixa de diálogo **Opções** . (Se o usuário conectado for um usuário convidado (ou seja joe@outlook.com ,), você deverá marcar a caixa e adicionar o nome de domínio do AD atual ou a ID de locatário como parte das opções. Consulte [autenticação universal com o banco de dados SQL e o Azure Synapse Analytics (suporte do SSMS para MFA)](../database/authentication-mfa-ssms-overview.md). E clique em **Conectar**.  
5. Quando a caixa de diálogo **Conectar-se à sua conta** aparecer, forneça a conta e a senha de sua identidade do Azure Active Directory. Nenhuma senha será necessária se um usuário fizer parte de um domínio federado com o Azure AD.

   ![2mfa-sign-in](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > Para a Autenticação Universal com uma conta que não exige o MFA, você se conecta neste momento. Para usuários que precisam do MFA, continue com as seguintes etapas:
   >  

6. Devem aparecer duas caixas de diálogo de configuração de MFA. Essa operação única depende da configuração de administrador de MFA e, portanto, pode ser opcional. Para um domínio habilitado para MFA, essa etapa às vezes é predefinida (por exemplo, o domínio requer que os usuários usem um cartão inteligente e um PIN).

   ![3mfa-setup](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. A segunda caixa de diálogo única possível permite que você selecione os detalhes do seu método de autenticação. As possíveis opções são configuradas pelo administrador.

   ![4mfa-verify-1](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. O Azure Active Directory envia as informações de confirmação para você. Quando receber o código de verificação, insira-o na caixa **Digite o código de verificação** e clique em **Entrar**.

   ![5mfa-verify-2](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Quando a verificação for concluída, o SSMS se conectará normalmente considerando as credenciais válidas e o acesso ao firewall.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da autenticação multifator, consulte [autenticação universal com o banco de dados SQL, sql instância gerenciada e Azure Synapse (suporte do SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).  
- Conceder acesso a outros a seu banco de dados: [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](logins-create-manage.md)  
- Verifique se outras pessoas podem se conectar por meio do firewall: [Configurar uma regra de firewall no nível de servidor usando o portal do Azure](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure)  
- Ao usar a autenticação **Active Directory - Universal com MFA**, o rastreamento ADAL está disponível a partir do [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desativado por padrão, você pode ativar o rastreamento ADAL usando o menu **Ferramentas**, no menu **Opções**, em **Serviços do Azure**, **Nuvem do Azure**, **Nível de rastreamento de janela de saída ADAL** e, em seguida, habilitando **Saída** no menu **Exibição**. Os rastreamentos estão disponíveis na janela de saída ao selecionar a **opção do Active Directory do Azure**.
