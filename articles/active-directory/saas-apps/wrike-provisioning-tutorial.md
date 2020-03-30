---
title: 'Tutorial: Configure wrike para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Wrike.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064182"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Tutorial: Configure wrike para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas que você executa no Wrike e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários ou grupos para wrike.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do serviço de provisionamento de usuários Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas freqüentes, consulte [Automate provisionamento e desprovisionamento de usuários para aplicativos de software como serviço (SaaS) com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos gerais de uso do Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino wrike](https://www.wrike.com/price/)
* Uma conta de usuário em Wrike com permissões de administração

## <a name="assign-users-to-wrike"></a>Atribuir usuários ao Wrike
O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, decida quais usuários ou grupos no Azure AD precisam ter acesso ao Wrike. Em seguida, atribua esses usuários ou grupos ao Wrike seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Dicas importantes para atribuir usuários ao Wrike

* Recomendamos que você designe um único usuário Azure AD ao Wrike para testar a configuração automática de provisionamento do usuário. Outros usuários ou grupos podem ser atribuídos posteriormente.

* Quando você atribui um usuário ao Wrike, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a função Default Access são excluídos do provisionamento.

## <a name="set-up-wrike-for-provisioning"></a>Configurar Wrike para provisionamento

Antes de configurar o Wrike para provisionamento automático do usuário com o Azure AD, você precisa habilitar o provisionamento do System for Cross-domain Identity Management (SCIM) no Wrike.

1. Faça login no seu [console de admin Wrike](https://www.Wrike.com/login/). Vá para a sua id inquilino. Selecione **aplicativos & integrações**.

    ![Aplicativos & Integrações](media/Wrike-provisioning-tutorial/admin.png)

2.  Vá para **Azure AD** e selecione-o.

    ![AD do Azure](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selecione SCIM. Copie a **URL base**.

    ![URL base](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selecione **API** > **Azure SCIM**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Um pop-up se abre. Digite a mesma senha que você criou anteriormente para criar uma conta.

    ![Wrike Criar token](media/Wrike-provisioning-tutorial/password.png)

6.  Copie o **Token Secreto**e cole-o no Azure AD. Selecione **Salvar** para concluir a configuração de provisionamento no Wrike.

    ![Token de acesso permanente](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Adicione Wrike da galeria

Antes de configurar o Wrike para provisionamento automático do usuário com o Azure AD, adicione wrike da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

Para adicionar wrike da galeria de aplicativos Azure AD, siga estas etapas.

1. No [portal Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Wrike**, selecione **Wrike** no painel de resultados e, em seguida, **selecione Adicionar** para adicionar o aplicativo.

    ![Wrike na lista de resultados](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Configure o provisionamento automático do usuário para wrike 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários ou grupos no Wrike com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Para habilitar o login único baseado em SAML para Wrike, siga as instruções no [tutorial de signon-on único do Wrike](wrike-tutorial.md). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Configure o provisionamento automático do usuário para Wrike no Azure AD

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **aplicativos corporativos** > **Todos os aplicativos**.

    ![Todos os aplicativos](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Wrike**.

    ![O link do Wrike na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Modo de provisionamento definido como Automático](common/provisioning-automatic.png)

5. Na seção Credenciais de Admin, insira os valores **de URL base** e de **token de acesso permanente** recuperados anteriormente em URL de **inquilino** e **Token Secreto,** respectivamente. Selecione **Conexão de teste** para garantir que o Azure AD possa se conectar ao Wrike. Se a conexão falhar, certifique-se de que sua conta Wrike tenha permissões de administração e tente novamente.

    ![URL do inquilino + token](common/provisioning-testconnection-tenanturltoken.png)

7. Na caixa **De e-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione **enviar uma notificação de e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Notificação por email](common/provisioning-notification-email.png)

8. Selecione **Salvar**.

9. Na seção **Mapeamentos,** selecione **Sincronizar usuários de diretórioativo ativo do Azure para Wrike**.

    ![Mapeamentos de usuários do Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Revise os atributos do usuário sincronizados do Azure AD para wrike na seção Mapeamentos de **atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Wrike para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Atributos do usuário wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Para configurar filtros de escopo, siga as instruções no [tutorial do filtro Scoping](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento Azure AD para Wrike, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status de provisionamento alternado](common/provisioning-toggle-on.png)

13. Defina os usuários ou grupos que você deseja prover ao Wrike escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **Escopo** na seção **Configurações.** A sincronização inicial leva mais tempo para ser realizado do que as sincronizações subseqüentes. Para obter mais informações sobre quanto tempo leva para os usuários ou grupos proverem, consulte [quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **Status atual** para monitorar o progresso e seguir links para o seu relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Wrike. Para obter mais informações, consulte [Verifique o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
