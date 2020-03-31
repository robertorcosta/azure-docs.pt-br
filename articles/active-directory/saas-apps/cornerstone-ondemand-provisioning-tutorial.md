---
title: 'Tutorial: Configurar a Cornerstone OnDemand para provisionamento automático de usuário com Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Cornerstone OnDemand.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058390"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configurar a Cornerstone OnDemand para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem realizadas no Cornerstone OnDemand e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários ou grupos para o Cornerstone OnDemand.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de provisionamento de usuários Azure AD. Para obter informações sobre o que esse serviço faz, como ele funciona e perguntas freqüentes, consulte [Automate provisioning e deprovisionamento de usuários para aplicativos de software como serviço (SaaS) com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tem:

* Um locatário do Azure AD.
* Um inquilino da Cornerstone OnDemand.
* Uma conta de usuário no Cornerstone OnDemand com permissões de administração.

> [!NOTE]
> A integração de provisionamento Azure AD conta com o [serviço web Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Este serviço está disponível para as equipes da Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Adicionar Cornerstone OnDemand do Mercado Azure

Antes de configurar o Cornerstone OnDemand para provisionamento automático do usuário com o Azure AD, adicione o Cornerstone OnDemand do Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Cornerstone OnDemand do Marketplace, siga estas etapas.

1. No [portal Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Diretório Ativo do Azure](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione ** Novo aplicativo ** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Cornerstone OnDemand** e selecione **Cornerstone OnDemand** no painel de resultados. Para adicionar o aplicativo, **selecione Adicionar**.

    ![Cornerstone OnDemand na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Atribuir usuários à Cornerstone OnDemand

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, decida quais usuários ou grupos no Azure AD precisam acessar o Cornerstone OnDemand. Para atribuir esses usuários ou grupos ao Cornerstone OnDemand, siga as instruções em [Atribuir um usuário ou grupo a um aplicativo corporativo](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Dicas importantes para atribuir usuários à Cornerstone OnDemand

* Recomendamos que você designe um único usuário Azure AD ao Cornerstone OnDemand para testar a configuração de provisionamento automático do usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Quando você atribuir um usuário ao Cornerstone OnDemand, selecione qualquer função específica de aplicativo válida, se disponível, na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configure o provisionamento automático do usuário para o Cornerstone OnDemand

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD. Use-o para criar, atualizar e desativar usuários ou grupos no Cornerstone OnDemand com base em atribuições de usuário ou grupo no Azure AD.

Para configurar o provisionamento automático do usuário para o Cornerstone OnDemand no Azure AD, siga estas etapas.

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **aplicativos Corporativos** > **Todos os aplicativos** > **Cornerstone OnDemand**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Cornerstone OnDemand**.

    ![O link Cornerstone OnDemand na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento onDemand da Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção Credenciais de **administração,** digite o nome de usuário do admin, a senha de administração e o domínio da conta do Seu Cornerstone OnDemand:

    * Na caixa Nome de usuário do **Admin,** preencha o domínio ou o nome de usuário da conta de administração no inquilino do Cornerstone OnDemand. Um exemplo é contoso\admin.

    * Na caixa **Senha de Admin,** preencha a senha que corresponde ao nome de usuário do admin.

    * Na caixa **Domínio,** preencha a URL do serviço web do inquilino Cornerstone OnDemand. Por exemplo, o serviço `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`está localizado em , `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`e para Contoso o domínio é . Para obter mais informações sobre como recuperar a URL do serviço web, consulte [este pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Depois de preencher as caixas mostradas na Etapa 5, selecione **Conexão de teste** para garantir que o Azure AD possa se conectar ao Cornerstone OnDemand. Se a conexão falhar, certifique-se de que sua conta Cornerstone OnDemand tenha permissões de administração e tente novamente.

    ![Conexão de teste ondemand da Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Na caixa **E-mail** de notificação, digite o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione **enviar uma notificação de e-mail quando ocorrer uma falha** na caixa de seleção.

    ![E-mail de notificação ondemand da Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selecione **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com a Cornerstone OnDemand**.

    ![Sincronização cornerstone onDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Revise os atributos do usuário sincronizados do Azure AD para o Cornerstone OnDemand na seção Mapeamentos de **atributos.** Os atributos selecionados como propriedades **Correspondentes** são utilizados para corresponder as contas de usuários na Cornerstone OnDemand para operações de atualização. Para salvar quaisquer alterações, **selecione Salvar**.

    ![Mapeamentos de atributos da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Para configurar filtros de escopo, siga as instruções no [tutorial do filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento Azure AD para O Saque-Largo, na seção **Configurações,** altere o **status de provisionamento** para **On**.

    ![Status de provisionamento onDemand da Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Defina os usuários ou grupos que você deseja provisionar para o Cornerstone OnDemand. Na seção **Configurações,** selecione os valores desejados no **Escopo**.

    ![Escopo de demanda da Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![Poupança de poupança de demanda da Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Esta operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **Escopo** na seção **Configurações.** A sincronização inicial leva mais tempo para ser realizado do que sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD seja executado. 

Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento Azure AD no Cornerstone OnDemand.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

O atributo Cornerstone OnDemand **posição** espera um valor que corresponde às funções no portal da Cornerstone OnDemand. Para obter uma lista de valores de **posição** válidos, vá para Editar o registro do **usuário > estrutura de organização > posição** no portal Cornerstone OnDemand.

![Registro de usuário de edição de edição de edição de provisionamento onDemand da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posição de provisionamento onDemand da Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Lista de posições de provisionamento onDemand da Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
