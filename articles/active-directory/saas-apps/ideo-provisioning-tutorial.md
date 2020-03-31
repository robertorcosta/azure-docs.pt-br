---
title: 'Tutorial: Configure iDEO para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para IDEO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: f5f163109d648a4fc021b41325c6d585a5a7a3e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057559"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Tutorial: Configure IDEO para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no IDEO e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para IDEO.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino iDEO](https://www.shape.space/product/pricing)
* Uma conta de usuário no IDEO | Forma com permissões de admin.

## <a name="assign-users-to-ideo"></a>Atribuir usuários ao IDEO

O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o IDEO. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao IDEO seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>Dicas importantes para atribuir usuários ao IDEO

* Recomenda-se que um único usuário Azure AD seja designado ao IDEO para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao IDEO, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="set-up-ideo-for-provisioning"></a>Configurar IDEO para provisionamento

Antes de configurar o IDEO para provisionamento automático do usuário com o Azure AD, você precisará recuperar algumas informações de provisionamento do IDEO.

1. Para **contato com o Contact** IDEO em . productsupport@ideo.com Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do seu aplicativo IDEO no portal Azure. 

## <a name="add-ideo-from-the-gallery"></a>Adicione IDEO da galeria

Para configurar o IDEO para provisionamento automático do usuário com o Azure AD, você precisa adicionar iDEO da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **IDEO**, selecione **IDEO** no painel de resultados. 

    ![IDEO na lista de resultados](common/search-new-app.png)

5. Selecione o botão **'Inscreva-se para IDEO',** que irá redirecioná-lo para a página de login do IDEO. 

    ![IDEO OIDC Adicionar](media/ideo-provisioning-tutorial/signup.png)

6. Como o IDEO é um aplicativo OpenIDConnect, opte por fazer login no IDEO usando sua conta de trabalho da Microsoft.

    ![Login IDEO OIDC](media/ideo-provisioning-tutorial/login.png)

7. Após uma autenticação bem-sucedida, aceite o prompt de consentimento para a página de consentimento. O aplicativo será então adicionado automaticamente ao seu inquilino e você será redirecionado para sua conta IDEO.

    ![Consentimento IDEO OIDc](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>Configure o provisionamento automático do usuário para iDEO 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no IDEO com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para IDEO no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **IDEO**.

    ![O link IDEO na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://profile.ideo.com/api/scim/v2` na **URL do inquilino**. Insira o valor que você recuperou da equipe de suporte do IDEO em **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao IDEO. Se a conexão falhar, certifique-se de que sua conta IDEO tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários de diretório ativo do Azure para IDEO**.

    ![Mapeamentos de usuários IDEO](media/ideo-provisioning-tutorial/usermappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o IDEO na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no IDEO para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário IDEO](media/ideo-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento Azure AD para IDEO, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](media/ideo-provisioning-tutorial/groupmappings.png)

12. Defina os usuários e/ou grupos que você gostaria de proprovisionar ao IDEO escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](media/ideo-provisioning-tutorial/groupattributes.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no IDEO.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)


