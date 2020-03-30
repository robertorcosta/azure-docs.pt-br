---
title: 'Tutorial: Configure o TheOrgWiki para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063128"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Tutorial: Configure o TheOrgWiki para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no TheOrgWiki e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o TheOrgWiki.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino orgWiki](https://www.theorgwiki.com/welcome/).
* Uma conta de usuário no TheOrgWiki com permissões de administração.

## <a name="assign-users-to-theorgwiki"></a>Atribuir usuários ao TheOrgWiki

O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o TheOrgWiki. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao TheOrgWiki seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Dicas importantes para atribuir usuários ao TheOrgWiki

* Recomenda-se que um único usuário azure AD seja designado ao TheOrgWiki para testar a configuração de provisionamento automático do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao TheOrgWiki, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configurar o TheOrgWiki para provisionamento

Antes de configurar o TheOrgWiki para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento de SCIM no TheOrgWiki.

1. Faça login no seu [console de administrador TheOrgWiki](https://www.theorgwiki.com/login/). Clique em **Admin Console**.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. No console de admin, clique na **guia Configurações .** 

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navegue até **contas de serviço**.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Clique em **+Conta de Serviço**. Em **Tipo de conta de serviço,** selecione Baseado em **Token**. Clique em **Salvar**.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copie os **Tokens Ativos**. Esse valor será inserido no campo Token Secreto na guia Provisionamento do seu aplicativo TheOrgWiki no portal Azure.
     
    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Adicione TheOrgWiki da galeria

Para configurar o TheOrgWiki para provisionamento automático do usuário com o Azure AD, você precisa adicionar o TheOrgWiki da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite TheOrgWiki**, selecione **TheOrgWiki** no painel de resultados. 

    ![TheOrgWiki na lista de resultados](common/search-new-app.png)

5. Selecione o botão **'Inscreva-se para TheOrgWiki',** que irá redirecioná-lo para a página de login do TheOrgWiki. 

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  No canto superior direito, selecione **Login**.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Como o TheOrgWiki é um aplicativo OpenIDConnect, opte por fazer login no OrgWiki usando sua conta de trabalho da Microsoft.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Após uma autenticação bem-sucedida, o aplicativo será automaticamente adicionado ao seu inquilino e você será redirecionado para sua conta TheOrgWiki.

    ![OrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configure o provisionamento automático do usuário para o TheOrgWiki 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no TheOrgWiki com base em atribuições de usuário e/ou grupo no Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o TheOrgWiki no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TheOrgWiki**.

    ![O link OrgWiki na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` na **URL do inquilino**. 

    Exemplo: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> O **Valor de Subdomínio** só pode ser definido durante o processo inicial de inscrição para theOrgWiki.
 
6. Insira o valor do token no campo **Token Secreto,** que você recuperou anteriormente do TheOrgWiki. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao TheOrgWiki. Se a conexão falhar, certifique-se de que sua conta TheOrgWiki tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Salvar**.

9. Na seção **Mapeamentos,** selecione **Sincronizar usuários do Diretório Ativo do Azure para TheOrgWiki**.

    ![Mapeamentos de usuários do TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Revise os atributos do usuário sincronizados do Azure AD ao TheOrgWiki na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no TheOrgWiki para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário theOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento Azure AD para TheOrgWiki, altere o **status de provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você gostaria de provisionar para orgWiki escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial leva mais tempo para ser realizado do que as sincronizações subseqüentes. Para obter mais informações sobre quanto tempo levará para os usuários e/ou grupos provisionarem, consulte [quanto tempo levará para provisionar os usuários](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **Status atual** para monitorar o progresso e seguir links para o seu relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no TheOrgWiki. Para obter mais informações, consulte [Verifique o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como revisar registros e obter relatórios sobre atividades de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).