---
title: 'Tutorial: Configure elium para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para elium.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058451"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Tutorial: Configure Elium para provisionamento automático do usuário

Este tutorial mostra como configurar o Elium e o Azure Active Directory (Azure AD) para provisionar e desprovisionar automaticamente usuários ou grupos para o Elium.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de provisionamento de usuários Azure AD. Para obter detalhes importantes sobre o que esse serviço faz e como ele funciona, e para perguntas frequentes, consulte [Automating e deprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter os termos gerais de uso para recursos do Azure na pré-visualização, consulte [Termos de Uso Suplementar escaneados para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino elium](https://www.elium.com/pricing/)
* Uma conta de usuário em Elium, com permissões de administração

## <a name="assigning-users-to-elium"></a>Atribuindo usuários ao Elium

O Azure AD usa um conceito chamado *atribuições* para determinar quais usuários recebem acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, decida quais usuários e grupos no Azure AD precisam ter acesso ao Elium. Em seguida, atribua esses usuários e grupos ao Elium seguindo as etapas em [Atribuir um usuário ou grupo a um aplicativo corporativo](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Dicas importantes para atribuir usuários ao Elium 

Recomendamos que você designe um único usuário Azure AD ao Elium para testar a configuração automática de provisionamento do usuário. Mais usuários e grupos podem ser atribuídos mais tarde.

Ao atribuir um usuário ao Elium, você deve selecionar uma função válida e específica do aplicativo (se houver disponível) na caixa de diálogo de atribuição. Os usuários que possuem a função **Default Access** são excluídos do provisionamento.

## <a name="set-up-elium-for-provisioning"></a>Configurar Elium para provisionamento

Antes de configurar o Elium para provisionamento automático do usuário com o Azure AD, você deve habilitar o provisionamento scim (System for Cross-domain Identity Management, gerenciamento de identidade de domínio cruzado) no Elium. Siga estas etapas:

1. Faça login no Elium e vá para Minhas**Configurações** **de Perfil** > .

    ![Item do menu de configurações em Elium](media/Elium-provisioning-tutorial/setting.png)

1. No canto inferior esquerdo, em **AVANÇADO,** selecione **Segurança**.

    ![Link de segurança em Elium](media/Elium-provisioning-tutorial/security.png)

1. Copie os valores de URL do **inquilino** e **do token secreto.** Você usará esses valores mais tarde, em campos correspondentes na guia **Provisionamento** do seu aplicativo Elium no portal Azure.

    ![Campos de URL e token secreto sinuosos em Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Adicione Elium da galeria

Para configurar o Elium para provisionamento automático do usuário com o Azure AD, você também deve adicionar elium da galeria de aplicativos Azure AD à sua lista de aplicativos gerenciados de software como serviço (SaaS). Siga estas etapas:

1. No [portal Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![Item do menu do Diretório Ativo do Azure](common/select-azuread.png)

1. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

     ![Lâmina de aplicativos Azure AD Enterprise](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior do painel.

    ![Novo link de aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, **digite Elium,** selecione **Elium** na lista de resultados e, em seguida, **selecione Adicionar** para adicionar o aplicativo.

    ![Caixa de pesquisa da galeria](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Configure o provisionamento automático do usuário para Elium

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e grupos no Elium, com base em atribuições de usuários e grupos no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único para Elium com base no SAML (Security Assertion Markup Language, linguagem de marcação de afirmação de segurança) seguindo as instruções no tutorial de login único do [Elium](Elium-tutorial.md). Você pode configurar o login único independentemente do provisionamento automático do usuário, embora os dois recursos se complementem.

Para configurar o provisionamento automático do usuário para Elium no Azure AD, siga estas etapas:

1. Faça login no [portal Azure,](https://portal.azure.com)selecione **aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Lâmina de aplicativos Azure AD Enterprise](common/enterprise-applications.png)

1. Na lista de aplicativos, escolha **Elium**.

    ![Lista de aplicativos na lâmina de aplicativos Corporativos](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento na lâmina de aplicativos Enterprise](common/provisioning.png)

1. Defina o **modo de provisionamento** como **automático**.

    ![Configuração automática para modo de provisionamento](common/provisioning-automatic.png)

1. Na seção Credenciais de **admin,** digite ** \<o inquilinoURL\>/scim/v2** no campo URL do **inquilino.** (O **inquilinoURL** é o valor recuperado anteriormente do console de admin Elium.) Digite também o valor **do token** Elium Secret no campo **Token Secreto.** Por fim, selecione **Conexão de teste** para verificar se o Azure AD pode se conectar ao Elium. Se a conexão falhar, certifique-se de que sua conta Elium tenha permissões de administração e tente novamente.

    ![Url inquilino e campos de token secreto em credenciais de admin](common/provisioning-testconnection-tenanturltoken.png)

1. No campo **E-mail** de notificação, digite o endereço de e-mail de uma pessoa ou grupo que receberá as notificações de erro de provisionamento. Em seguida, **selecione enviar uma notificação de e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Email de notificação](common/provisioning-notification-email.png)

1. Clique em **Salvar**.

1. Na seção **Mapeamentos,** selecione **Sincronizar usuários de diretório ativo do Azure para Elium**.

    ![Sincronizar link para mapear usuários de AD do Azure para Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Revise os atributos do usuário sincronizados do Azure AD para elium na seção Mapeamentos de **atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Elium para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Mapeamentos de atributos entre Azure AD e Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Para configurar filtros de escopo, siga as instruções no [tutorial do filtro Scoping](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento Azure AD para Elium, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status de provisionamento definido como On](common/provisioning-toggle-on.png)

1. Defina os usuários e grupos que você gostaria de provisionar para Elium selecionando os valores desejados na caixa de lista de sossego **escopo** na seção **Configurações.**

    ![Caixa de lista de escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![Salvar botão para configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações.** Esse processo inicial de sincronização leva mais tempo do que sincronizações posteriores. Para obter mais informações sobre o tempo necessário para o provisionamento, consulte [quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Use a seção **Status atual** para monitorar o progresso e siga links para o seu relatório de atividade de provisionamento. O relatório de atividade de provisionamento descreve todas as ações realizadas pelo serviço de provisionamento Azure AD no Elium. Para obter mais informações, consulte [Verifique o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
