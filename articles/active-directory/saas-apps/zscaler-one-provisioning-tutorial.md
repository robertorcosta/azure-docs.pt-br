---
title: 'Tutorial: configurar o Zscaler One para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Zscaler One.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77064165"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Tutorial: configurar o Zscaler One para o provisionamento automático de usuário

Este tutorial demonstra as etapas a serem executadas no Zscaler One e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e grupos para o Zscaler One.

> [!NOTE]
> Este tutorial descreve um conector baseado no serviço de provisionamento de usuário do Azure Active Directory. Para obter informações sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS (software como serviço) com o Azure Active Directory](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você possui:

* Um locatário do Azure AD.
* Um locatário do Zscaler One.
* Uma conta de usuário no Zscaler One com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API do Zscaler One SCIM. Essa API está disponível para Zscaler um desenvolvedor para contas com o pacote Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Adicionar o Zscaler One do Azure Marketplace

Antes de configurar o Zscaler One para o provisionamento automático de usuário com o Azure AD, adicione o Zscaler um do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Zscaler One do Marketplace, siga estas etapas.

1. No [Portal do Microsoft Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler One** e selecione **Zscaler One** no painel de resultados. Para adicionar o aplicativo, selecione **Adicionar**.

    ![Zscaler One na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Atribuir usuários ao Zscaler One

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure Active Directory são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Zscaler One. Para atribuir esses usuários ou grupos ao Zscaler One, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Dicas importantes para atribuir usuários ao Zscaler One

* Recomendamos que você atribua um único usuário do Azure AD ao Zscaler um para testar a configuração automática de provisionamento de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Ao atribuir um usuário ao Zscaler One, selecione qualquer função específica do aplicativo válida, se disponível, na caixa de diálogo atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurar o provisionamento automático de usuário para o Zscaler One

Esta seção aborda as etapas para configurar o serviço de provisionamento do Azure Active Directory. Use-o para criar, atualizar e desabilitar usuários ou grupos no Zscaler um com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Você também pode habilitar o logon único baseado em SAML para o Zscaler One. Siga as instruções no [tutorial de logon único do Zscaler One](zscaler-One-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Zscaler One no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**  >  **todos os aplicativos**  >  **Zscaler um**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler One**.

    ![O link Zscaler One na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Zscaler um provisionamento](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Zscaler um modo de provisionamento](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Na seção **credenciais de administrador** , preencha as caixas **URL do locatário** e **token secreto** com as configurações para sua conta do Zscaler uma, conforme descrito na etapa 6.

6. Para obter a URL do locatário e o token secreto, acesse **Administração**  >  **configurações de autenticação** na interface do usuário do portal do Zscaler One. Em **Tipo de Autenticação**, selecione **SAML**.

    ![Zscaler uma configuração de autenticação](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selecione **Configurar SAML** para abrir as opções **Configurar SAML** .

    ![Zscaler um configurar SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selecione **habilitar provisionamento baseado em scim** para obter as configurações na URL de **base** e no **token de portador**. Em seguida, salve as configurações. Copie a configuração da **URL base** para a **URL do locatário** no portal do Azure. Copie a configuração de **token de portador** para o **token secreto** no portal do Azure.

7. Depois de preencher as caixas mostradas na etapa 5, selecione **testar conexão** para garantir que o Azure ad possa se conectar ao Zscaler One. Se a conexão falhar, verifique se o Zscaler uma conta tem permissões de administrador e tente novamente.

    ![Zscaler uma conexão de teste](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Na caixa **Email de Notificação**, insira o endereço de email da pessoa ou grupo que deve receber as notificações do erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Zscaler um email de notificação](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selecione **Salvar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Zscaler um**.

    ![Zscaler uma sincronização de usuário](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para Zscaler um na seção **mapeamentos de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Zscaler uma para operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Zscaler um dos atributos de usuário correspondentes](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Zscaler um**.

    ![Sincronização de um grupo do Zscaler](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para Zscaler um na seção **mapeamentos de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos em Zscaler um para operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Zscaler um atributo de grupo correspondente](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, siga as instruções fornecidas no [tutorial sobre filtros de escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o Zscaler One, na seção **configurações** , altere o **status de provisionamento** para **ativado**.

    ![Zscaler um status de provisionamento](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários ou grupos que você deseja provisionar para Zscaler um. Na seção **Configurações**, selecione os valores desejados em **Escopo**.

    ![Zscaler um escopo](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Zscaler um salvamento](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos em **Escopo**, na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações posteriores. Elas ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure Active Directory seja executado. 

É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Zscaler One.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
