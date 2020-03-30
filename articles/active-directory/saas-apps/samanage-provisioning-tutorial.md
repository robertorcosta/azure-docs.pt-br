---
title: 'Tutorial: Configurar Samanage para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para sagerenciar.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060421"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutorial: Configurar Samanage para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem realizadas no Samanage e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e grupos para sagerenciar.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de provisionamento de usuários Azure AD. Para obter informações sobre o que esse serviço faz, como ele funciona e perguntas freqüentes, consulte [Automate provisioning e deprovisionamento de usuários para aplicativos de software como serviço (SaaS) com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tem:

* Um locatário do Azure AD.
* Um [inquilino da Samanage](https://www.samanage.com/pricing/) com o pacote Profissional.
* Uma conta de usuário em Samanage com permissões de administração.

> [!NOTE]
> A integração de provisionamento Azure AD depende da [API Samanage Rest](https://www.samanage.com/api/). Esta API está disponível para desenvolvedores Samanage para contas com o pacote Profissional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Adicionar Samanage do Azure Marketplace

Antes de configurar o Samanage para provisionamento automático do usuário com o Azure AD, adicione o Samanage do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar Samanage do Marketplace, siga estes passos.

1. No [portal Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Diretório Ativo do Azure](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione ** Novo aplicativo ** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Sagerenciar** e **selecione Sagerenciar** no painel de resultados. Para adicionar o aplicativo, **selecione Adicionar**.

    ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Atribuir usuários ao Samanage

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, decida quais usuários ou grupos no Azure AD precisam ter acesso ao Samanage. Para atribuir esses usuários ou grupos ao Samanage, siga as instruções em [Atribuir um usuário ou grupo a um aplicativo corporativo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Dicas importantes para atribuir usuários ao Samanage

*    Hoje, as funções samanage são preenchidas automaticamente e dinamicamente na UI do portal Azure. Antes de atribuir funções samanage aos usuários, certifique-se de que uma sincronização inicial seja concluída contra o Samanage para recuperar as funções mais recentes em seu inquilino Samanage.

*    Recomendamos que você designe um único usuário Azure AD ao Samanage para testar sua configuração inicial de provisionamento automático do usuário. Você pode atribuir usuários e grupos adicionais mais tarde após o sucesso dos testes.

*    Quando você atribuir um usuário ao Samanage, selecione qualquer função específica de aplicativo válida, se disponível, na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configure o provisionamento automático do usuário para sagerenciar

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD. Use-o para criar, atualizar e desativar usuários ou grupos no Samanage com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Você também pode habilitar o login único baseado em SAML para Samanage. Siga as instruções no [tutorial de login único do Samanage](samanage-tutorial.md). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configure o provisionamento automático do usuário para Samanage no Azure AD

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **aplicativos corporativos** > **Todos os aplicativos** > **Samanage**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Samanage**.

    ![O link Samanage na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** insira sua URL de **inquilino** e **token secreto**do Samanage . Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Samanage. Se a conexão falhar, verifique se sua conta do Samanage tem permissões de Administrador e tente novamente.

    ![Conexão de teste Samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. Na caixa **E-mail** de notificação, digite o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione **enviar uma notificação de e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Sagerenciar e-mail de notificação](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Selecione **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para Sagerenciar**.

    ![Sagerenciar a sincronização do usuário](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Revise os atributos de usuário que são sincronizados do Azure AD para Samanage na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Samanage em operações de atualização. Para salvar quaisquer alterações, **selecione Salvar**.

    ![Samanage atributos de usuário correspondentes](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Para habilitar mapeamentos de grupo, na seção **Mapeamentos**, selecione **Sincronizar grupos do Azure Active Directory com Samanage**.

    ![Sincronização de grupo Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Defina **Habilitado** para **Sim** para sincronizar grupos. Revise os atributos do grupo que são sincronizados do Azure AD para Samanage na seção**Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Samanage em operações de atualização. Para salvar quaisquer alterações, **selecione Salvar**.

    ![Samanage atributos de grupo correspondente](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Para configurar filtros de escopo, siga as instruções no [tutorial do filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para Samanage, na seção **Configurações,** altere o **status de provisionamento** para **On**.

    ![Status de provisionamento samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Defina os usuários ou grupos que você deseja prover ao Samanage. Na seção **Configurações,** selecione os valores desejados no **Escopo**. Ao selecionar a opção **Sincronizar todos os usuários e grupos,** considere as limitações descritas na seção seguinte "Limitações do Conector".

    ![Escopo Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![Samanage Salvar](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Esta operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **Escopo** na seção **Configurações.** A sincronização inicial leva mais tempo para ser realizado do que sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD seja executado. 

Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento Azure AD no Samanage.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

Se você selecionar a opção **Sincronizar todos os usuários e grupos** e configurar um valor para o atributo Samanage **roles,** o valor o **valor Padrão se nulo (é opcional)** deve ser expresso no seguinte formato:

- {"displayName":"role"}, onde a função é o valor padrão que você deseja.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
