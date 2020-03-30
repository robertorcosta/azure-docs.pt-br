---
title: 'Tutorial: Configure o Zscaler One para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Zscaler One.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064165"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Tutorial: Configure o Zscaler One para provisionamento automático do usuário

Este tutorial demonstra as etapas a serem realizadas no Zscaler One e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e grupos para o Zscaler One.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de provisionamento de usuários Azure AD. Para obter informações sobre o que esse serviço faz, como ele funciona e perguntas freqüentes, consulte [Automate provisioning e deprovisionamento de usuários para aplicativos de software como serviço (SaaS) com o Azure Active Directory](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tem:

* Um locatário do Azure AD.
* Um inquilino zscaler 1.
* Uma conta de usuário no Zscaler One com permissões de administração.

> [!NOTE]
> A integração de provisionamento Azure AD depende da API Zscaler One SCIM. Esta API está disponível para desenvolvedores Zscaler One para contas com o pacote Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Adicionar Zscaler One do Azure Marketplace

Antes de configurar o Zscaler One para provisionamento automático do usuário com o Azure AD, adicione o Zscaler One do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Zscaler One do Marketplace, siga estes passos.

1. No [portal Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Diretório Ativo do Azure](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione ** Novo aplicativo ** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **O Zscaler One** e selecione **O Zscaler One** no painel de resultados. Para adicionar o aplicativo, **selecione Adicionar**.

    ![Zscaler One na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Atribuir usuários ao Zscaler One

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, decida quais usuários ou grupos no Azure AD precisam acessar o Zscaler One. Para atribuir esses usuários ou grupos ao Zscaler One, siga as instruções em [Atribuir um usuário ou grupo a um aplicativo corporativo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Dicas importantes para atribuir usuários ao Zscaler One

* Recomendamos que você designe um único usuário Azure AD ao Zscaler One para testar a configuração automática de provisionamento do usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Ao atribuir um usuário ao Zscaler One, selecione qualquer função específica de aplicativo válida, se disponível, na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configure o provisionamento automático do usuário para o Zscaler One

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD. Use-o para criar, atualizar e desativar usuários ou grupos no Zscaler One com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Você também pode habilitar o login único baseado em SAML para o Zscaler One. Siga as instruções no tutorial de aprovação única do [Zscaler One](zscaler-One-tutorial.md). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configure o provisionamento automático do usuário para zscaler One no Azure AD

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **aplicativos Corporativos** > **Todos os aplicativos** > **Zscaler One**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler One**.

    ![O link Zscaler One na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Modo de provisionamento Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Na seção Credenciais de **Administração,** preencha as caixas **de URL** e **Token Secreto** do Inquilino com as configurações da sua conta Zscaler One, conforme descrito na Etapa 6.

6. Para obter a URL do inquilino e o token secreto, vá para**Configurações de Autenticação** de **Administração** > no portal Zscaler One. Em **Tipo de Autenticação**, selecione **SAML**.

    ![Configurações de autenticação do Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selecione **Configurar SAML** para abrir as opções **Configurar SAML.**

    ![Zscaler One Configure SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selecione **Ativar provisionamento baseado em SCIM** para obter as configurações em **URL base** e **token do portador**. Em seguida, salve as configurações. Copie a configuração **de URL base** para URL do **inquilino** no portal Azure. Copie a configuração **do Portador Token** **para O Token Secreto** no portal Azure.

7. Depois de preencher as caixas mostradas na Etapa 5, selecione **Conexão de teste** para garantir que o Azure AD possa se conectar ao Zscaler One. Se a conexão falhar, certifique-se de que sua conta Zscaler One tenha permissões de administração e tente novamente.

    ![Conexão de teste Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Na caixa **E-mail** de notificação, digite o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione **enviar uma notificação de e-mail quando ocorrer uma falha** na caixa de seleção.

    ![E-mail de notificação zscaler one](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selecione **Salvar**.

10. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para Zscaler One**.

    ![Sincronização do usuário Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Revise os atributos do usuário sincronizados do Azure AD ao Zscaler One na seção **Mapeamentos de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Zscaler One para operações de atualização. Para salvar quaisquer alterações, **selecione Salvar**.

    ![Zscaler One atributos correspondentes ao usuário](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para Zscaler One**.

    ![Sincronização do grupo Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Revise os atributos de grupo sincronizados do Azure AD para o Zscaler One na seção **Mapeamentos de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder aos grupos no Zscaler One para operações de atualização. Para salvar quaisquer alterações, **selecione Salvar**.

    ![Atributos do grupo de correspondência Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, siga as instruções no [tutorial do filtro de escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o serviço de provisionamento Azure AD para zscaler One, na seção **Configurações,** altere o **status de provisionamento** para **On**.

    ![Status de provisionamento Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários ou grupos que você deseja provisionar ao Zscaler One. Na seção **Configurações,** selecione os valores desejados no **Escopo**.

    ![Zscaler One Scope](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![Zscaler One Save](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **Escopo** na seção **Configurações.** A sincronização inicial leva mais tempo para ser realizado do que sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD seja executado. 

Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento Azure AD no Zscaler One.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
