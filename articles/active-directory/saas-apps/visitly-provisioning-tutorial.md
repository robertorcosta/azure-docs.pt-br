---
title: 'Tutorial: configurar o visitado para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para visitar.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77063136"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Tutorial: configurar o visitado para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas executadas em visita e Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente os usuários ou grupos para visitar.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS (software como serviço) com Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário visitado](https://www.visitly.io/pricing/)
* Uma conta de usuário em visite as permissões de administrador

## <a name="assign-users-to-visitly"></a>Atribuir usuários para visitarem 

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure Active Directory são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso para visitar. Em seguida, atribua esses usuários ou grupos para visitar o seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Dicas importantes para atribuir usuários para visitarem 

* Recomendamos que você atribua um único usuário do Azure AD para visitar para testar a configuração automática de provisionamento de usuário. Usuários ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário para visitar, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.

## <a name="set-up-visitly-for-provisioning"></a>Configurar de forma visitada para provisionamento

Antes de configurar o visitado para o provisionamento automático de usuário com o Azure AD, você precisa habilitar o sistema para o provisionamento do SCIM (gerenciamento de identidade entre domínios) em visite.

1. Entre para [visitar](https://app.visitly.io/login). Selecione **integrações**  >  **host Synchronization**.

    ![Sincronização do host](media/Visitly-provisioning-tutorial/login.png)

2. Selecione a seção **Azure ad** .

    ![Seção do Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Copie a **chave de API**. Esses valores são inseridos na caixa **token secreto** na guia **provisionamento** do seu aplicativo visitado no portal do Azure.

    ![Chave de API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Adicionar o visitado da Galeria

Para configurar o visitado para o provisionamento automático de usuário com o Azure AD, adicione o visitado da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

Para adicionar o visitado da Galeria de aplicativos do Azure AD, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **visitado**, selecione **visitar** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar o aplicativo.

    ![Visitly na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Configurar o provisionamento automático de usuário para visitar 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos em visita com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Para habilitar o logon único baseado em SAML para o acessado, siga as instruções no [tutorial de logon único do acesse](Visitly-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Configurar o provisionamento automático de usuário para visitar o Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**  >  **todos os aplicativos**.

    ![Todos os aplicativos](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Visitly**.

    ![O link do Visitly na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento definido como automático](common/provisioning-automatic.png)

5. Na seção credenciais de administrador, insira os `https://api.visitly.io/v1/usersync/SCIM` valores de **chave de API** e recuperados anteriormente na URL do **locatário** e no **token secreto**, respectivamente. Selecione **testar conexão** para garantir que o Azure ad possa se conectar ao visite. Se a conexão falhar, verifique se sua conta visitada tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. Na caixa **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Notificação por email](common/provisioning-notification-email.png)

7. Selecione **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para visitarem**.

    ![Mapeamentos de usuário visitados](media/visitly-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para visitar a seção **mapeamentos de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário em visitado para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Atributos de usuário visitados](media/visitly-provisioning-tutorial/userattribute.png)

10. Para configurar filtros de escopo, siga as instruções no [tutorial filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para visitar, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

12. Defina os usuários ou grupos que você deseja provisionar para visitar o visitado escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos em **Escopo**, na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo leva para o provisionamento de usuários ou grupos, consulte [quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **status atual** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD em visitado. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

Visitado não dá suporte a exclusões rígidas. Tudo é somente exclusão reversível.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
