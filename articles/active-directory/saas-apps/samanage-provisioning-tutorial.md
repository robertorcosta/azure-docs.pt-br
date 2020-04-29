---
title: 'Tutorial: Configurar Samanage para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Samanage.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060421"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutorial: Configurar Samanage para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem executadas no Samanage e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e grupos no Samanage.

> [!NOTE]
> Este tutorial descreve um conector que é criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter informações sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS (software como serviço) com Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tenha:

* Um locatário do Azure AD.
* Um [locatário do Samanage](https://www.samanage.com/pricing/) com o pacote profissional.
* Uma conta de usuário no Samanage com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API REST do Samanage](https://www.samanage.com/api/). Essa API está disponível para desenvolvedores de Samanage para contas com o pacote profissional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Adicionar o Samanage do Azure Marketplace

Antes de configurar o Samanage para o provisionamento automático de usuário com o Azure AD, adicione o Samanage do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Samanage do Marketplace, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone de Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione ** Novo aplicativo ** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Samanage** e selecione **Samanage** no painel de resultados. Para adicionar o aplicativo, selecione **Adicionar**.

    ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Atribuir usuários ao Samanage

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Samanage. Para atribuir esses usuários ou grupos ao Samanage, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Dicas importantes para atribuir usuários ao Samanage

*    Hoje, as funções Samanage são preenchidas de forma automática e dinâmica na interface do usuário do portal do Azure. Antes de atribuir funções Samanage a usuários, verifique se uma sincronização inicial foi concluída em relação a Samanage para recuperar as funções mais recentes em seu locatário Samanage.

*    Recomendamos que você atribua um único usuário do Azure AD ao Samanage para testar a configuração inicial de provisionamento automático de usuário. Você pode atribuir usuários e grupos adicionais posteriormente depois que os testes forem bem-sucedidos.

*    Ao atribuir um usuário ao Samanage, selecione qualquer função específica do aplicativo válida, se disponível, na caixa de diálogo atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configurar o provisionamento automático de usuário para o Samanage

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD. Use-o para criar, atualizar e desabilitar usuários ou grupos no Samanage com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Você também pode habilitar o logon único baseado em SAML para o Samanage. Siga as instruções no [tutorial de logon único do Samanage](samanage-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configurar o provisionamento automático de usuário para Samanage no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos** > **empresariais todos os aplicativos** > **Samanage**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Samanage**.

    ![O link do Samanage na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário** do Samanage e o **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Samanage. Se a conexão falhar, verifique se sua conta do Samanage tem permissões de Administrador e tente novamente.

    ![Conexão de teste do Samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. Na caixa **email de notificação** , insira o endereço de email da pessoa ou grupo para receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação do Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Selecione **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Samanage**.

    ![Sincronização de usuário do Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Revise os atributos de usuário que são sincronizados do Azure AD para Samanage na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Samanage em operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Samanage atributos de usuário correspondentes](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Para habilitar mapeamentos de grupo, na seção **Mapeamentos**, selecione **Sincronizar grupos do Azure Active Directory com Samanage**.

    ![Sincronização do grupo de Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Defina **Habilitado** para **Sim** para sincronizar grupos. Revise os atributos do grupo que são sincronizados do Azure AD para Samanage na seção**Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Samanage em operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Samanage atributos de grupo correspondentes](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Para configurar filtros de escopo, siga as instruções no [tutorial filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Samanage, na seção **configurações** , altere o **status de provisionamento** para **ativado**.

    ![Status de provisionamento do Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Defina os usuários ou grupos que você deseja provisionar para Samanage. Na seção **configurações** , selecione os valores desejados no **escopo**. Ao selecionar a opção **sincronizar todos os usuários e grupos** , considere as limitações descritas na seção a seguir "limitações do conector".

    ![Escopo de Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Quando estiver pronto para provisionar, selecione **salvar**.

    ![Samanage salvar](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD seja executado. 

Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Samanage.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

Se você selecionar a opção **sincronizar todos os usuários e grupos** e configurar um valor para o atributo **funções** Samanage, o valor na caixa **valor padrão se nulo (é opcional)** deverá ser expresso no seguinte formato:

- {"displayName": "role"}, em que role é o valor padrão desejado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
