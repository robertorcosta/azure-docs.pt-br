---
title: 'Tutorial: Configure zscaler beta para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Zscaler Beta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 505cd7a3350c937885f0f03268ef326a4f784258
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062716"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Tutorial: Configure Zscaler Beta para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler Beta e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Zscaler Beta.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../active-directory-saas-app-provisioning.md).
>


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

* Um locatário do Azure AD
* Um inquilino Zscaler Beta
* Uma conta de usuário em Zscaler Beta com permissões de administração

> [!NOTE]
> A integração de provisionamento Azure AD conta com a API Zscaler Beta SCIM, que está disponível para desenvolvedores Zscaler Beta para contas com o pacote Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Adicionar o Zscaler Beta da galeria

Antes de configurar o Zscaler Beta para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Zscaler Beta da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler Beta na galeria de aplicativos azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler Beta**, selecione **Zscaler Beta** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zscaler Beta na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Atribuindo usuários ao Zscaler Beta

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Zscaler Beta. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Zscaler Beta seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Dicas importantes para atribuir usuários ao Zscaler Beta

* Recomenda-se que um único usuário Azure AD seja atribuído ao Zscaler Beta para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Zscaler Beta, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Configuração do provisionamento automático do usuário para Zscaler Beta

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Zscaler Beta com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para Zscaler Beta, seguindo as instruções fornecidas no [tutorial de login único do Zscaler Beta](zscaler-beta-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para Zscaler Beta no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com) e selecione **Aplicativos Corporativos,** selecione **Todos os aplicativos**e selecione **Zscaler Beta**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler Beta**.

    ![O link do Zscaler Beta na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Na seção Credenciais de **Administração,** insira a **URL do inquilino** e o sinal **secreto** da sua conta Zscaler Beta conforme descrito na Etapa 6.

6. Para obter a **URL do inquilino** e o **Token Secreto,** navegue até **as configurações de autenticação do > de administração** na interface do usuário do portal Zscaler Beta e clique em **SAML** em **Tipo de Autenticação**.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Clique em **Configurar SAML** para abrir as opções **SAML de configuração.**

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Selecione **Ativar provisionamento baseado em SCIM** para recuperar a **URL base** e **o Token bearer**e, em seguida, salvar as configurações. Copie a **URL base** para url **do inquilino**e o **Token do Portador** para o **Token Secreto** no portal Azure.

7. Ao preencher os campos mostrados no Passo 5, clique em **Conexão de teste** para garantir que o Azure AD possa se conectar ao Zscaler Beta. Se a conexão falhar, certifique-se de que sua conta Zscaler Beta tenha permissões de administração e tente novamente.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos,** selecione Sincronizar usuários do diretório ativo do **Azure para Zscaler Beta**.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Revise os atributos do usuário sincronizados do Azure AD ao Zscaler Beta na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Zscaler Beta para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **Mapeamentos,** selecione **Sincronizar Grupos de diretórios ativos do Azure para Zscaler Beta**.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Revise os atributos de grupo sincronizados do Azure AD para o Zscaler Beta na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no Zscaler Beta para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o serviço de provisionamento Azure AD para Zscaler Beta, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários e/ou grupos que você gostaria de provisionar ao Zscaler Beta escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento Beta zscaler](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Zscaler Beta.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
