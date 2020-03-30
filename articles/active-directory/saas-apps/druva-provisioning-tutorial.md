---
title: 'Tutorial: Configure druva para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para druva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 3d1bb0bcbc0df98d7a884004cf96fe9810589185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058103"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Tutorial: Configure Druva para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Diretório Ativo Druva e Azure (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para Druva.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino druva.](https://www.druva.com/products/pricing-plans/)
* Uma conta de usuário em Druva com permissões de administração.

## <a name="assigning-users-to-druva"></a>Atribuindo usuários ao Druva

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam ter acesso ao Druva. Uma vez decidido, você pode atribuir esses usuários e/ou grupos a Druva seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Dicas importantes para atribuir usuários à Druva

* Recomenda-se que um único usuário Azure AD seja designado ao Druva para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Druva, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-druva-for-provisioning"></a>Configuração Druva para provisionamento

Antes de configurar o Druva para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento SCIM no Druva.

1. Faça login no seu [Console De Dmin Druva](https://console.druva.com). Navegue até **Druva > inSync**.

    ![Druva Admin Console](media/druva-provisioning-tutorial/menubar.png)

2. Navegue para **gerenciar** > **implantações** > **usuários**.

    ![Druva Adicionar SCIM](media/druva-provisioning-tutorial/manage.png)

3.  Navegue até **Configurações**. Clique **em Gerar token**.

    ![Druva Adicionar SCIM](media/druva-provisioning-tutorial/settings.png)

4.  Copie o valor do **token Auth.** Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do seu aplicativo Druva no portal Azure.
    
    ![Druva Adicionar SCIM](media/druva-provisioning-tutorial/auth.png)

## <a name="add-druva-from-the-gallery"></a>Adicione Druva da galeria

Para configurar o Druva para provisionamento automático do usuário com o Azure AD, você precisa adicionar druva da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Druva na galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Druva**, selecione **Druva** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Druva na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Configuração do provisionamento automático do usuário para Druva 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos em Druva com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para Druva, seguindo as instruções fornecidas no [tutorial de signon-on druva único](druva-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para Druva no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Druva**.

    ![O link do Druva na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5.  Na seção Credenciais de `https://apis.druva.com/insync/scim` Admin, entrada na **URL do inquilino**. Insira o valor **do token Auth** no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Druva. Se a conexão falhar, certifique-se de que sua conta Druva tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione **Enviar uma notificação por e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários de diretórioativo ativo do Azure para Druva**.

    ![Mapeamentos do Usuário druva](media/druva-provisioning-tutorial/usermapping.png)

9. Revise os atributos do usuário sincronizados do Azure AD para druva na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário em Druva para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do Usuário Druva](media/druva-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento Azure AD para Druva, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de prover para Druva escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividadede provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD em Druva.

    Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Limitações do conector

* Druva requer **e-mail** como atributo obrigatório. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como revisar registros e obter relatórios sobre atividades de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
