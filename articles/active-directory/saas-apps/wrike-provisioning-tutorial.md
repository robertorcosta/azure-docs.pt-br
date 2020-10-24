---
title: 'Tutorial: configurar o Wrike para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Wrike.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 76b5f279294dda0d2b1c01bf8cdd803390b04cec
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520023"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Tutorial: configurar o Wrike para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas que você executa em Wrike e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários ou grupos no Wrike.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS (software como serviço) com Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Wrike](https://www.wrike.com/price/)
* Uma conta de usuário no Wrike com permissões de administrador

## <a name="assign-users-to-wrike"></a>Atribuir usuários ao Wrike
O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure Active Directory são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Wrike. Em seguida, atribua esses usuários ou grupos ao Wrike seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Dicas importantes para atribuir usuários ao Wrike

* Recomendamos que você atribua um único usuário do Azure AD ao Wrike para testar a configuração automática de provisionamento de usuário. Usuários ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Wrike, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.

## <a name="set-up-wrike-for-provisioning"></a>Configurar o Wrike para provisionamento

Antes de configurar o Wrike para o provisionamento automático de usuário com o Azure AD, você precisa habilitar o sistema de provisionamento do SCIM (gerenciamento de identidade entre domínios) no Wrike.

1. Entre no console do [administrador do Wrike](https://www.Wrike.com/login/). Vá para sua ID de locatário. Selecione **aplicativos & integrações**.

    ![Integrações de & de aplicativos](media/Wrike-provisioning-tutorial/admin.png)

2.  Vá para **Azure ad** e selecione-o.

    ![AD do Azure](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selecione SCIM. Copie a **URL base**.

    ![URL base](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selecione **API**  >  **Azure scim**.

    ![SCIM do Azure](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Um pop-up é aberto. Insira a mesma senha que você criou anteriormente para criar uma conta.

    ![Wrike criar token](media/Wrike-provisioning-tutorial/password.png)

6.  Copie o **token secreto**e cole-o no Azure AD. Selecione **salvar** para concluir a configuração de provisionamento no Wrike.

    ![Token de acesso permanente](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Adicionar o Wrike da Galeria

Antes de configurar o Wrike para o provisionamento automático de usuário com o Azure AD, adicione o Wrike da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Wrike da Galeria de aplicativos do Azure AD, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Wrike**, selecione **Wrike** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar o aplicativo.

    ![Wrike na lista de resultados](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Configurar o provisionamento automático de usuário para o Wrike 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no Wrike com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Para habilitar o logon único baseado em SAML para o Wrike, siga as instruções no [tutorial de logon único do Wrike](wrike-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Configurar o provisionamento automático de usuário para Wrike no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**  >  **todos os aplicativos**.

    ![Todos os aplicativos](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Wrike**.

    ![O link do Wrike na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento definido como automático](common/provisioning-automatic.png)

5. Na seção credenciais de administrador, insira a **URL base** e os valores de **token de acesso permanentes** recuperados anteriormente na **URL do locatário** e no **token secreto**, respectivamente. Selecione **testar conexão** para garantir que o Azure ad possa se conectar ao Wrike. Se a conexão falhar, verifique se sua conta do Wrike tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. Na caixa **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Notificação por email](common/provisioning-notification-email.png)

8. Selecione **Salvar**.

9. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Wrike**.

    ![Mapeamentos de usuário Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Examine os atributos de usuário que são sincronizados do Azure AD para o Wrike na seção **mapeamentos de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Wrike para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Atributos de usuário do Wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Para configurar filtros de escopo, siga as instruções no [tutorial filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD para o Wrike, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

13. Defina os usuários ou grupos que você deseja provisionar para Wrike escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos em **Escopo**, na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo leva para o provisionamento de usuários ou grupos, consulte [quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **status atual** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Wrike. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)