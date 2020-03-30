---
title: 'Tutorial: Configure o Visitly para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para visitativamente.
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
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063136"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Tutorial: Configure visitly para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas que você executa no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários ou grupos para o Visitly.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do serviço de provisionamento de usuários Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas freqüentes, consulte [Automate provisionamento e desprovisionamento de usuários para aplicativos de software como serviço (SaaS) com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos gerais de uso do Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino visitante](https://www.visitly.io/pricing/)
* Uma conta de usuário em Visitly com permissões de administração

## <a name="assign-users-to-visitly"></a>Atribuir usuários ao Visitly 

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, decida quais usuários ou grupos no Azure AD precisam acessar o Visitly. Em seguida, atribua esses usuários ou grupos ao Visitly seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Dicas importantes para atribuir usuários ao Visitly 

* Recomendamos que você designe um único usuário azure AD para visitar a configuração de provisionamento automático do usuário. Outros usuários ou grupos podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Visitly, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a função Default Access são excluídos do provisionamento.

## <a name="set-up-visitly-for-provisioning"></a>Configurar o Visitly para provisionamento

Antes de configurar o Visitly para provisionamento automático do usuário com o Azure AD, você precisa habilitar o provisionamento do System for Cross-domain Identity Management (SCIM) no Visitly.

1. Faça login em [Visitly](https://app.visitly.io/login). Selecione **integrações** > **sincronização do host**.

    ![Sincronização do host](media/Visitly-provisioning-tutorial/login.png)

2. Selecione a seção **Azure AD.**

    ![Seção Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Copie a **tecla API**. Esses valores são inseridos na caixa **Token Secreto** na guia **Provisionamento** do aplicativo Visitly no portal Azure.

    ![Chave de API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Adicionar visiteda galeria

Para configurar o Visitly para provisionamento automático do usuário com o Azure AD, adicione o Visitly da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

Para adicionar Visitly da galeria de aplicativos Azure AD, siga estas etapas.

1. No [portal Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Visitly**, selecione **Visitly** no painel de resultados e, em seguida, **selecione Adicionar** para adicionar o aplicativo.

    ![Visitly na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Configure o provisionamento automático do usuário para o Visitly 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários ou grupos no Visitly com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Para habilitar o login único baseado em SAML para visitly, siga as instruções no [tutorial de login único do Visitly](Visitly-tutorial.md). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Configure o provisionamento automático do usuário para visitly no Azure AD

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **aplicativos corporativos** > **Todos os aplicativos**.

    ![Todos os aplicativos](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Visitly**.

    ![O link do Visitly na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Modo de provisionamento definido como Automático](common/provisioning-automatic.png)

5. Na seção Credenciais de Admin, `https://api.visitly.io/v1/usersync/SCIM` insira os valores de chave e **API** recuperados anteriormente em URL de **inquilino** e **Token Secreto,** respectivamente. Selecione **Conexão de teste** para garantir que o Azure AD possa se conectar ao Visitly. Se a conexão falhar, certifique-se de que sua conta visitativamente tenha permissões de administração e tente novamente.

    ![URL do inquilino + token](common/provisioning-testconnection-tenanturltoken.png)

6. Na caixa **De e-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione **enviar uma notificação de e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Notificação por email](common/provisioning-notification-email.png)

7. Selecione **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para visitativamente**.

    ![Mapeamentos de usuários visitando](media/visitly-provisioning-tutorial/usermapping.png)

9. Revise os atributos do usuário sincronizados do Azure AD para visitativamente na seção Mapeamentos de **atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Visitly para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Atributos de usuário visitantes](media/visitly-provisioning-tutorial/userattribute.png)

10. Para configurar filtros de escopo, siga as instruções no [tutorial do filtro Scoping](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento Azure AD para Visitly, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status de provisionamento alternado](common/provisioning-toggle-on.png)

12. Defina os usuários ou grupos que você deseja prover para Visitly escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **Escopo** na seção **Configurações.** A sincronização inicial leva mais tempo para ser realizado do que as sincronizações subseqüentes. Para obter mais informações sobre quanto tempo leva para os usuários ou grupos proverem, consulte [quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **Status atual** para monitorar o progresso e seguir links para o seu relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Visitly. Para obter mais informações, consulte [Verifique o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

A visita não suporta exclusões duras. Tudo é apenas soft delete.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
