---
title: 'Tutorial: Configurar o Tableau Online para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Tableau Online.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064196"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Configurar o Tableau Online para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem realizadas no Tableau Online e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e grupos para o Tableau Online.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de provisionamento de usuários Azure AD. Para obter informações sobre o que esse serviço faz, como ele funciona e perguntas freqüentes, consulte [Automate provisioning e deprovisionamento de usuários para aplicativos de software como serviço (SaaS) com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tem:

*   Um locatário do Azure AD.
*   Um [inquilino Tableau Online](https://www.tableau.com/).
*   Uma conta de usuário no Tableau Online com permissões de administração.

> [!NOTE]
> A integração de provisionamento Azure AD depende da [API Tableau Online Rest](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Esta API está disponível para desenvolvedores tableau online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Adicionar Tableau Online do Azure Marketplace
Antes de configurar o Tableau Online para provisionamento automático do usuário com o Azure AD, adicione o Tableau Online do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Tableau Online do Marketplace, siga estes passos.

1. No [portal Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Diretório Ativo do Azure](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione ** Novo aplicativo ** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Tableau Online** e **selecione Tableau Online** no painel de resultados. Para adicionar o aplicativo, **selecione Adicionar**.

    ![Tableau Online na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Atribuir usuários ao Tableau Online

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, decida quais usuários ou grupos no Azure AD precisam acessar o Tableau Online. Para atribuir esses usuários ou grupos ao Tableau Online, siga as instruções em [Atribuir um usuário ou grupo a um aplicativo corporativo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Dicas importantes para atribuir usuários ao Tableau Online

*   Recomendamos que você designe um único usuário Azure AD ao Tableau Online para testar a configuração automática de provisionamento do usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

*   Ao atribuir um usuário ao Tableau Online, selecione qualquer função específica de aplicativo válida, se disponível, na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configure o provisionamento automático do usuário para o Tableau Online

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD. Use-o para criar, atualizar e desativar usuários ou grupos no Tableau Online com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Você também pode habilitar o login único baseado em SAML para o Tableau Online. Siga as instruções no tutorial de login único do [Tableau Online](tableauonline-tutorial.md). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configure o provisionamento automático do usuário para Tableau Online no Azure AD

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **aplicativos Corporativos** > **Todos os aplicativos** > **Tableau Online**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Tableau Online**.

    ![O link Tableau Online na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Modo de provisionamento on-line do Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção Credenciais de **Administração,** insira o domínio, o nome de usuário do admin, a senha de administração e a URL de conteúdo da sua conta Tableau Online:

   * Na caixa **Domínio,** preencha o subdomínio com base no Passo 6.

   * Na caixa Nome de usuário do **Admin,** preencha o nome de usuário da conta de administração no seu Inquilino Clarizen. Um exemplo é admin@contoso.com.

   * Na caixa Senha de **Administração,** preencha a senha da conta de administração que corresponde ao nome de usuário do admin.

   * Na caixa **URL de conteúdo,** preencha o subdomínio com base no Passo 6.

6. Depois de fazer login na sua conta administrativa do Tableau Online, você pode obter os valores para **URL de domínio** e **conteúdo** a partir da URL da página de administração.

    * O **Domínio** da conta do Tableau Online pode ser copiado desta parte da URL:

        ![Domínio Online do Tableau](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * A **URL de conteúdo** da sua conta Tableau Online pode ser copiada a partir desta seção. É um valor definido durante a configuração da conta. Neste exemplo, o valor é "contoso":

        ![URL de conteúdo on-line do Tableau](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Seu **domínio** pode ser diferente do mostrado aqui.

7. Depois de preencher as caixas mostradas na Etapa 5, selecione **Conexão de teste** para garantir que o Azure AD possa se conectar ao Tableau Online. Se a conexão falhar, certifique-se de que sua conta Tableau Online tenha permissões de administração e tente novamente.

    ![Conexão de teste on-line do Tableau](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Na caixa **E-mail** de notificação, digite o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione **enviar uma notificação de e-mail quando ocorrer uma falha** na caixa de seleção.

    ![E-mail de notificação on-line do Tableau](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selecione **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Tableau**.

    ![Sincronização de usuário do Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Revise os atributos do usuário sincronizados do Azure AD para o Tableau Online na seção Mapeamentos de **atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Para salvar quaisquer alterações, **selecione Salvar**.

    ![Atributos de usuário correspondentes do Tableau Online](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Tableau**.

    ![Sincronização de grupo Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Revise os atributos de grupo sincronizados do Azure AD para o Tableau Online na seção Mapeamentos de **atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Para salvar quaisquer alterações, **selecione Salvar**.

    ![Atributos do grupo de correspondência Tableau Online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Para configurar filtros de escopo, siga as instruções no [tutorial do filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento Azure AD para Tableau Online, na seção **Configurações,** altere o **status de provisionamento** para **On**.

    ![Status de provisionamento on-line do Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Defina os usuários ou grupos que você deseja provisionar ao Tableau Online. Na seção **Configurações,** selecione os valores desejados no **Escopo**.

    ![Escopo on-line do Tableau](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![Tableau Online Save](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Esta operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **Escopo** na seção **Configurações.** A sincronização inicial leva mais tempo para ser realizado do que sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD seja executado. 

Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento Azure AD no Tableau Online.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
