---
title: 'Tutorial: Configurar a Cornerstone OnDemand para provisionamento automático de usuário com Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para a base OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058390"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configurar a Cornerstone OnDemand para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem executadas na base OnDemand e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários ou grupos para a base OnDemand.

> [!NOTE]
> Este tutorial descreve um conector que é criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter informações sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS (software como serviço) com Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

O cenário descrito neste tutorial pressupõe que você tenha:

* Um locatário do Azure AD.
* Um locatário da base OnDemand.
* Uma conta de usuário na base OnDemand com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende do [serviço Web da base OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Esse serviço está disponível para as equipes da base OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Adicionar base OnDemand do Azure Marketplace

Antes de configurar a base OnDemand para o provisionamento automático de usuário com o Azure AD, adicione a base OnDemand do Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar a base OnDemand do Marketplace, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone de Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **base OnDemand** e selecione **base OnDemand** no painel de resultados. Para adicionar o aplicativo, selecione **Adicionar**.

    ![Cornerstone OnDemand na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Atribuir usuários ao base OnDemand

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso à base OnDemand. Para atribuir esses usuários ou grupos ao fundamento OnDemand, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Dicas importantes para atribuir usuários à Cornerstone OnDemand

* Recomendamos que você atribua um único usuário do Azure AD à base OnDemand para testar a configuração automática de provisionamento de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Ao atribuir um usuário à base OnDemand, selecione qualquer função específica do aplicativo válida, se disponível, na caixa de diálogo atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurar o provisionamento automático de usuário para a base OnDemand

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD. Use-o para criar, atualizar e desabilitar usuários ou grupos na base OnDemand com base em atribuições de usuário ou de grupo no Azure AD.

Para configurar o provisionamento automático de usuário para a base OnDemand no Azure AD, siga estas etapas.

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais** > **todos os aplicativos** > **base OnDemand**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Cornerstone OnDemand**.

    ![O link da base OnDemand na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento do fundamento OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **credenciais de administrador** , insira o nome de usuário do administrador, a senha do administrador e o domínio da conta do seu fundamento OnDemand:

    * Na caixa **nome de usuário do administrador** , preencha o domínio ou o nome de usuário da conta do administrador em seu locatário do base OnDemand. Um exemplo é contoso\admin.

    * Na caixa **senha do administrador** , preencha a senha que corresponde ao nome de usuário do administrador.

    * Na caixa **domínio** , preencha a URL do serviço Web do locatário da base OnDemand. Por exemplo, o serviço está localizado em `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`e, para contoso, o domínio é `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Para obter mais informações sobre como recuperar a URL do serviço Web, consulte [este PDF](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Depois de preencher as caixas mostradas na etapa 5, selecione **testar conexão** para garantir que o Azure ad possa se conectar ao fundamento OnDemand. Se a conexão falhar, verifique se sua conta da base OnDemand tem permissões de administrador e tente novamente.

    ![Conexão de teste da base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Na caixa **email de notificação** , insira o endereço de email da pessoa ou grupo para receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação da base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selecione **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com a Cornerstone OnDemand**.

    ![A sincronização da base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Examine os atributos de usuário que são sincronizados do Azure AD para a base OnDemand na seção **mapeamentos de atributo** . Os atributos selecionados como propriedades **Correspondentes** são utilizados para corresponder as contas de usuários na Cornerstone OnDemand para operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Mapeamentos de atributo da base do OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Para configurar filtros de escopo, siga as instruções no [tutorial filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD para a base OnDemand, na seção **configurações** , altere o **status de provisionamento** para **ativado**.

    ![Status de provisionamento do fundamento OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Defina os usuários ou grupos que você deseja provisionar para a base OnDemand. Na seção **configurações** , selecione os valores desejados no **escopo**.

    ![Escopo da base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando estiver pronto para provisionar, selecione **salvar**.

    ![Salvar da base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD seja executado. 

Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure AD na base OnDemand.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

O atributo Cornerstone OnDemand **posição** espera um valor que corresponde às funções no portal da Cornerstone OnDemand. Para obter uma lista de valores de **posição** válidos, vá para **Editar registro de usuário > estrutura de organização > posição** no portal da base OnDemand.

![Configuração de edição do registro de usuário do fundamento OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posição de provisionamento do fundamento OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Lista de posição de provisionamento do fundamento OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
