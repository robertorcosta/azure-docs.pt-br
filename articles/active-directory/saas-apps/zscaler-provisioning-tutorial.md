---
title: 'Tutorial: configurar o Zscaler para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8add1f57b566d746d464c1ca165938fc112a9784
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062701"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Tutorial: configurar o Zscaler para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Zscaler.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Prerequisites

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

* Um locatário do Azure AD.
* Um locatário do Zscaler.
* Uma conta de usuário no Zscaler com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API Zscaler SCIM, que está disponível para desenvolvedores Zscaler para contas com o pacote Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Adicionar o Zscaler da galeria

Antes de configurar o Zscaler para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Zscaler da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler**, selecione **Zscaler** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zscaler na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Atribuindo usuários ao Zscaler

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Zscaler. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Zscaler seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Dicas importantes para atribuir usuários ao Zscaler

* É recomendável que um único usuário do Azure AD seja atribuído ao Zscaler para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Zscaler, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Configurando o provisionamento automático de usuário para o Zscaler

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Zscaler com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Zscaler, seguindo as instruções fornecidas no [tutorial de logon único do Zscaler](zscaler-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Zscaler no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Zscaler**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler**.

    ![O link do Zscaler na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário** e o **token secreto** da sua conta do Zscaler, conforme descrito na etapa 6.

6. Para obter a **URL do locatário** e o **token secreto**, navegue até **Administração > configurações de autenticação** na interface do usuário do portal do Zscaler e clique em **SAML** em **tipo de autenticação**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Clique em **Configurar SAML** para abrir as opções de **configuração de SAML** .

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Selecione **habilitar provisionamento baseado em scim** para recuperar a **URL base** e o **token de portador**e, em seguida, salve as configurações. Copie a **URL base** para a **URL do locatário**e o token de **portador** para o **token secreto** no portal do Azure.

7. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Zscaler. Se a conexão falhar, verifique se sua conta do Zscaler tem permissões de administrador e tente novamente.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/test-connection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/notification.png)

9. Clique em **Save** (Salvar).

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Zscaler**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Zscaler na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Zscaler para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Zscaler**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o Zscaler na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Zscaler para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o Zscaler, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários e/ou grupos que você deseja provisionar para o Zscaler escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Zscaler.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
