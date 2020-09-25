---
title: 'Tutorial: configurar o TheOrgWiki para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o TheOrgWiki.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 0a72ea413e7298c8e6cb622cb985e6516435b451
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91255743"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Tutorial: configurar o TheOrgWiki para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no TheOrgWiki e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no TheOrgWiki.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do OrgWiki](https://www.theorgwiki.com/welcome/).
* Uma conta de usuário no TheOrgWiki com permissões de administrador.

## <a name="assign-users-to-theorgwiki"></a>Atribuir usuários ao TheOrgWiki

O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao TheOrgWiki. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao TheOrgWiki seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Dicas importantes para atribuir usuários ao TheOrgWiki

* É recomendável que um único usuário do Azure AD seja atribuído ao TheOrgWiki para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao TheOrgWiki, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configurar o TheOrgWiki para provisionamento

Antes de configurar o TheOrgWiki para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no TheOrgWiki.

1. Entre no console do [administrador do TheOrgWiki](https://www.theorgwiki.com/login/). Clique em **console do administrador**.

    ![Captura de tela do wiki da org com o avatar do usuário e o console do administrador chamado.](media/theorgwiki-provisioning-tutorial/login.png)

2. No console do administrador do, clique na **guia Configurações**. 

    ![Captura de tela do console de administração do wiki da org com a guia Configurações denominada.](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navegue até **contas de serviço**.

    ![Captura de tela da página contas de serviço no console de administração do wiki da organização.](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Clique em **+ conta de serviço**. Em **tipo de conta de serviço**, selecione **baseado em token**. Clique em **Save** (Salvar).

    ![Captura de tela da caixa de diálogo Nova conta de serviço com o tipo de conta de serviço, baseado em token e opções de salvamento chamados.](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copie os **tokens ativos**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo TheOrgWiki no portal do Azure.
     
    ![Captura de tela da caixa de diálogo Gerenciar tokens para o provisionamento S C I M.](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Adicionar o TheOrgWiki da Galeria

Para configurar o TheOrgWiki para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o TheOrgWiki da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TheOrgWiki**, selecione **TheOrgWiki** no painel de resultados. 

    ![TheOrgWiki na lista de resultados](common/search-new-app.png)

5. Selecione o botão **inscrever-se no TheOrgWiki** , que o redirecionará para a página de logon do TheOrgWiki. 

    ![Captura de tela da página de logon do wiki da org com a URL chamada](media/theorgwiki-provisioning-tutorial/image00.png)

6.  No canto superior direito, selecione **logon**.

    ![Captura de tela do canto superior direito da página de logon com a opção de logon chamada out.](media/theorgwiki-provisioning-tutorial/image02.png)

7. Como TheOrgWiki é um aplicativo OpenIDConnect, opte por fazer logon no OrgWiki usando sua conta corporativa da Microsoft.

    ![Captura de tela da página de entrada do wiki da organização com a opção Entrar com a conta da Microsoft chamada out.](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Após uma autenticação bem-sucedida, o aplicativo será adicionado automaticamente ao seu locatário e você será redirecionado para sua conta do TheOrgWiki.

    ![OrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configurar o provisionamento automático de usuário para o TheOrgWiki 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TheOrgWiki com base em atribuições de usuário e/ou grupo no Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para TheOrgWiki no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TheOrgWiki**.

    ![O link do OrgWiki na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` a **URL de locatário**. 

    Exemplo: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> O **valor de subdomínio** só pode ser definido durante o processo de inscrição inicial para TheOrgWiki.
 
6. Insira o valor do token no campo de **token secreto** que você recuperou anteriormente de TheOrgWiki. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao TheOrgWiki. Se a conexão falhar, verifique se sua conta do TheOrgWiki tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Save** (Salvar).

9. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para TheOrgWiki**.

    ![Mapeamentos de usuário TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Examine os atributos de usuário que são sincronizados do Azure AD para o TheOrgWiki na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no TheOrgWiki para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD para o TheOrgWiki, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você deseja provisionar para o OrgWiki escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo levará para o provisionamento de usuários e/ou grupos, consulte [quanto tempo levará para provisionar usuários](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **status atual** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no TheOrgWiki. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).