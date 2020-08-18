---
title: 'Tutorial: Configurar o Tableau Online para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o tableau online.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: da89bf6cb336087f97341ca57a9b2545c966a053
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533409"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Configurar o Tableau Online para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem executadas no tableau online e no Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e grupos no tableau online.

> [!NOTE]
> Este tutorial descreve um conector baseado no serviço de provisionamento de usuário do Azure Active Directory. Para obter informações sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS (software como serviço) com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você possui:

*   Um locatário do Azure AD.
*   Um [locatário online do tableau](https://www.tableau.com/).
*   Uma conta de usuário no tableau online com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API REST do tableau online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Essa API está disponível para desenvolvedores do tableau online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Adicionar o tableau online do Azure Marketplace
Antes de configurar o tableau online para o provisionamento automático de usuário com o Azure AD, adicione o tableau online do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o tableau online do Marketplace, siga estas etapas.

1. No [Portal do Microsoft Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **tableau online** e selecione **tableau online** no painel de resultados. Para adicionar o aplicativo, selecione **Adicionar**.

    ![Tableau Online na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Atribuir usuários ao tableau online

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure Active Directory são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao tableau online. Para atribuir esses usuários ou grupos ao tableau online, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Dicas importantes para atribuir usuários ao Tableau Online

*   Recomendamos que você atribua um único usuário do Azure AD ao tableau online para testar a configuração automática de provisionamento de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

*   Quando você atribuir um usuário ao tableau online, selecione qualquer função específica do aplicativo válida, se disponível, na caixa de diálogo atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configurar o provisionamento automático de usuário para o tableau online

Esta seção aborda as etapas para configurar o serviço de provisionamento do Azure Active Directory. Use-o para criar, atualizar e desabilitar usuários ou grupos no tableau online com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Você também pode habilitar o logon único baseado em SAML para o tableau online. Siga as instruções no [tutorial de logon único do tableau online](tableauonline-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o tableau online no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**  >  **todos os aplicativos**  >  **tableau online**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Tableau Online**.

    ![O link do tableau online na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento online do tableau](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **credenciais de administrador** , insira o domínio, o nome de usuário administrador, a senha de administrador e a URL de conteúdo da sua conta do tableau online:

   * Na caixa **domínio** , preencha o subdomínio com base na etapa 6.

   * Na caixa **nome de usuário do administrador** , preencha o nome de usuário da conta do administrador em seu locatário do Clarizen. Um exemplo é admin@contoso.com.

   * Na caixa **senha do administrador** , preencha a senha da conta de administrador que corresponde ao nome de usuário do administrador.

   * Na caixa **URL do conteúdo** , preencha o subdomínio com base na etapa 6.

6. Depois de entrar em sua conta administrativa do tableau online, você pode obter os valores de URL de **domínio** e **conteúdo** da URL da página de administração.

    * O **Domínio** da conta do Tableau Online pode ser copiado desta parte da URL:

        ![Domínio do tableau online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * A **URL de conteúdo** para sua conta do tableau online pode ser copiada desta seção. É um valor que é definido durante a configuração da conta. Neste exemplo, o valor é "contoso":

        ![URL de conteúdo do tableau online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Seu **domínio** pode ser diferente daquele mostrado aqui.

7. Depois de preencher as caixas mostradas na etapa 5, selecione **testar conexão** para garantir que o Azure ad possa se conectar ao tableau online. Se a conexão falhar, verifique se sua conta do tableau online tem permissões de administrador e tente novamente.

    ![Conexão de teste do tableau online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Na caixa **Email de Notificação**, insira o endereço de email da pessoa ou grupo que deve receber as notificações do erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação do tableau online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Tableau**.

    ![Sincronização de usuário do tableau online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o tableau online na seção **mapeamentos de atributo** . Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Atributos de usuário correspondentes do tableau online](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Tableau**.

    ![Sincronização de grupo do tableau online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o tableau online na seção **mapeamentos de atributo** . Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Atributos do grupo de correspondência online do tableau](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Para configurar filtros de escopo, siga as instruções fornecidas no [tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o tableau online, na seção **configurações** , altere o **status de provisionamento** para **ativado**.

    ![Status de provisionamento do tableau online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Defina os usuários ou grupos que você deseja provisionar para tableau online. Na seção **Configurações**, selecione os valores desejados em **Escopo**.

    ![Escopo do tableau online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Salvar online do tableau](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos em **Escopo**, na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações posteriores. Elas ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure Active Directory seja executado. 

É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no tableau online.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
