---
title: 'Tutorial: configurar o software de recrutamento de coencontro para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o software de recrutamento coencontro.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: f427fb75cfaeda79b037c327992e4ad482a7e689
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058307"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Tutorial: configurar o software de recrutamento de coencontro para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas em coencontro software de recrutamento e Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para fazer a reunião de software de recrutamento.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário de software de recrutamento coencontro](https://www.comeet.co/)
* Uma conta de usuário em coencontro software de recrutamento com permissões de administrador.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Adicionar software de recrutamento de coencontro da Galeria

Antes de configurar o software de recrutamento de coencontro para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o software de recrutamento de coencontro da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o software de recrutamento de coencontro da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **software de recrutamento coencontro**, selecione o **software de recrutamento coencontro** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Comeet Recruiting Software na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Atribuindo usuários para o software de recrutamento coencontro

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso para o software de recrutamento coencontro. Depois de decidir, você pode atribuir esses usuários e/ou grupos para coatender ao software de recrutamento seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Dicas importantes para atribuir usuários para o software de recrutamento coencontro

* É recomendável que um único usuário do Azure AD seja atribuído ao software de recrutamento coencontro para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário para coencontrar o software de recrutamento, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Configurando o provisionamento automático de usuário para o software de recrutamento coencontro 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no software de recrutamento coencontro com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o software de recrutamento coencontro, seguindo as instruções fornecidas no [tutorial de logon único do software de recrutamento coencontro](comeetrecruitingsoftware-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o software de recrutamento coencontro no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Comeet recrutamento Software**.

    ![O link do Software de recrutamento Comeet na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário** e o **token secreto** de sua conta do software de recrutamento de seu encontro, conforme descrito na etapa 6.

6. No [console de administração de software de recrutamento coencontro](https://app.comeet.co/), navegue até **Coencontro > configurações > autenticação > Microsoft Azure**e copie o **token secreto para o valor da empresa** para o campo **token secreto** no Azure AD.

    ![Conencontro provisionamento de software de recrutamento](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao software de recrutamento coencontro. Se a conexão falhar, certifique-se de que sua conta de software de recrutamento de coencontro tenha permissões de administrador e tente novamente.

    ![Token](common/provisioning-testconnection-token.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários a serem coencontros**.

    ![Coencontro mapeamentos de usuário de software de recrutamento](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o software de recrutamento coencontro na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no software de recrutamento coencontro para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do grupo de software de recrutamento coencontro](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o software de recrutamento coencontro, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para coencontrar o software de recrutamento escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no software de recrutamento coencontro.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O software de recrutamento coencontro não oferece suporte a grupos no momento.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

