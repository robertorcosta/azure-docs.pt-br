---
title: 'Tutorial: Configure o Gateway de Recompensa para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Reward Gateway.
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
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 928d48907e43de5e65ca5604ff878bfb83d5e95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060965"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Tutorial: Configure o Gateway de Recompensa para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Reward Gateway e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Gateway de Recompensa.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* Um [inquilino do Gateway de Recompensa](https://www.rewardgateway.com/).
* Uma conta de usuário no Gateway de Recompensa com permissões de administração.

## <a name="assigning-users-to-reward-gateway"></a>Atribuindo usuários ao Gateway de Recompensa 

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Reward Gateway. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Reward Gateway seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo corporativo](../manage-apps/assign-user-or-group-access-portal.md).


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Dicas importantes para atribuir usuários ao Gateway de Recompensa 

* Recomenda-se que um único usuário azure AD seja designado ao Reward Gateway para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Gateway de recompensa, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-reward-gateway--for-provisioning"></a>Gateway de recompensa de configuração para provisionamento
Antes de configurar o Reward Gateway para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento SCIM no Reward Gateway.

1. Faça login no seu [console de admin do](https://rewardgateway.photoshelter.com/login/)Gateway de Recompensa . Clique em **Integrações**.

    ![Console de admin do Gateway de Recompensa](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Selecione **Minha Integração**.

    ![Console de admin do Gateway de Recompensa](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Copie os valores de **URL SCIM (v2)** e **OAuth Bearer Token**. Esses valores serão inseridos no campo URL do inquilino e no token secreto na guia Provisionamento do aplicativo Reward Gateway no portal Azure.

    ![Console de admin do Gateway de Recompensa](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Adicionar o Reward Gateway da Galeria

Para configurar o Reward Gateway para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Reward Gateway da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Gateway de Recompensa da galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite O Gateway de recompensa**, selecione O Gateway de **recompensa** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Reward Gateway na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Configuração do provisionamento automático do usuário para o Gateway de Recompensa  

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Reward Gateway com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o Reward Gateway, seguindo as instruções fornecidas no [tutorial de login único](reward-gateway-tutorial.md)do Gateway de Recompensa . O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o Reward Gateway no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Reward Gateway**.

    ![O link do Reward Gateway na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de **Admin,** insira os valores **de URL SCIM (v2)** e **OAuth Bearer Token** recuperados anteriormente em **URL de inquilino** e **Token Secreto,** respectivamente. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao gateway de recompensa. Se a conexão falhar, certifique-se de que sua conta de gateway de recompensa tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do azure Active Directory para o Gateway de recompensa**.

    ![Console de admin do Gateway de Recompensa](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Reward Gateway na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Gateway de recompensa para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Console de admin do Gateway de Recompensa](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento Azure AD para o Reward Gateway, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar para o Gateway de recompensa, escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividadede provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Reward Gateway.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

O Reward Gateway não oferece suporte ao provisionamento de grupo no momento.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

[Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
