---
title: 'Tutorial: Configure o Harness para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para aproveitar.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057797"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Tutorial: Configure harness para provisionamento automático do usuário

Neste artigo, você aprende como configurar o Azure Active Directory (Azure AD) para provisionar e desprovisionar automaticamente usuários ou grupos para aproveitar.

> [!NOTE]
> Este artigo descreve um conector que é construído em cima do serviço de provisionamento de usuários Azure AD. Para obter informações importantes sobre este serviço e respostas a perguntas frequentes, consulte [Automate provisioning e deprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste artigo pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino de Arreio](https://harness.io/pricing/)
* Uma conta de usuário no Harness com permissões *de administração*

## <a name="assign-users-to-harness"></a>Atribuir usuários ao Harness

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, decida quais usuários ou grupos no Azure AD precisam ter acesso ao Harness. Em seguida, você pode atribuir esses usuários ou grupos ao Harness seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo corporativo](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Dicas importantes para atribuir os usuários ao Harness

* Recomendamos que você designe um único usuário Azure AD para aproveitar para testar a configuração automática de provisionamento do usuário. Outros usuários ou grupos podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Harness, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo **Atribuição.** Os usuários com a *função Default Access* são excluídos do provisionamento.

## <a name="set-up-harness-for-provisioning"></a>Configurar o Harness para provisionamento

1. Faça login no seu console de controle de [arreios](https://app.harness.io/#/login)e, em seguida, vá para **o Gerenciamento contínuo de** > **acesso à**segurança .

    ![Console de arreios administrativos](media/harness-provisioning-tutorial/admin.png)

1. Selecione **as teclas de API**.

    ![Aproveitar o link de chaves da API](media/harness-provisioning-tutorial/apikeys.png)

1. Selecione **Adicionar a chave de API**. 

    ![Aproveitar adicionar link de chave da API](media/harness-provisioning-tutorial/addkey.png)

1. No **painel Adicionar chave Api,** faça o seguinte:

    ![Arreios adicionar painel de chave api](media/harness-provisioning-tutorial/title.png)
   
   a. Na caixa **Nome,** forneça um nome para a chave.  
   b. Nas **permissões herdadas da** lista suspensa, selecione uma opção. 
   
1. Selecione **Enviar**.

1. Copie a **chave** para uso posterior neste tutorial.

    ![Aproveitar criar token](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Adicionar Arreio da galeria

Antes de configurar o Harness para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Harness da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![Botão "Azure Active Directory"](common/select-azuread.png)

1. Selecione **aplicativos Corporativos** > **Todos os aplicativos**.

    ![O link "Todos os aplicativos"](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão “Novo aplicativo”](common/add-new-app.png)

1. Na caixa de pesquisa, **digite Aproveitar**, **selecione Aproveitar** na lista de resultados e, em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Aproveite na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Configure o provisionamento automático do usuário para aproveitar 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários ou grupos no Harness com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o Harness, seguindo as instruções no [tutorial de login único do Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). Você pode configurar um único login independente do provisionamento automático do usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final do Harness SCIM, consulte o artigo As chaves da [API](https://docs.harness.io/article/smloyragsm-api-keys) do Harness.

Para configurar o provisionamento automático do usuário para o Harness no Azure AD, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)selecione **Aplicativos Corporativos** > **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Harness**.

    ![O link Harness na lista de aplicativos](common/all-applications.png)

1. Selecione **Provisionamento**.

    ![O botão provisionamento](common/provisioning.png)

1. Na lista de isto do modo de **provisionamento,** selecione **Automático**.

    ![A lista de parada "Modo de provisionamento"](common/provisioning-automatic.png)

1. Em **Credenciais de Admin,** faça o seguinte:

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Na caixa URL do **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** **inquilino,** digite .  
   b. Na caixa **Deto secreto,** digite o valor de Token de Autenticação SCIM que você salvou na etapa 6 da seção "Configurar arreio para provisionamento".  
   c. Selecione **Conexão de teste** para garantir que o Azure AD possa se conectar ao Harness. Se a conexão falhar, certifique-se de que sua conta Harness tenha permissões *de administração* e tente novamente.

1. Na caixa **De e-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e, em seguida, selecione a **Notificação enviar um e-mail quando ocorrer uma falha** na caixa de seleção.

    ![A caixa "E-mail de notificação"](common/provisioning-notification-email.png)

1. Selecione **Salvar**.

1. Em **Mapeamentos,** selecione **Sincronizar usuários de diretório ativo do Azure para aproveitar**.

    ![Aproveite o link "Sincronizar usuários de diretórioativo ativo do Azure para aproveitar"](media/harness-provisioning-tutorial/usermappings.png)

1. Em **Mapeamentos de Atributos,** revise os atributos do usuário sincronizados do Azure AD para o Harness. Os atributos selecionados como *Matching* são usados para corresponder às contas de usuário no Harness para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Aproveite o painel "Mapeamentos de Atributos" do usuário](media/harness-provisioning-tutorial/userattributes.png)

1. Em **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para aproveitar**.

    ![Aproveite o link "Sincronizar grupos de diretórios ativos do Azure para aproveitar"](media/harness-provisioning-tutorial/groupmappings.png)

1. Em **Attribute Mappings**, revise os atributos de grupo sincronizados do Azure AD para o Harness. Os atributos selecionados como *propriedades correspondentes* são usados para corresponder aos grupos no Harness para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Painel do grupo de aproveitar "Mapeamentos de Atributos"](media/harness-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de escopo, consulte [provisionamento de aplicativos baseadoem em atributos com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Em **Configurações,** para habilitar o serviço de provisionamento Azure AD para Harness, alterne o switch **status de provisionamento** para **Ativado**.

    ![Mudança de status de provisionamento alternada para "On"](common/provisioning-toggle-on.png)

1. Em **Configurações,** na lista de parada **escopo,** selecione como você deseja sincronizar os usuários ou grupos que você está provisionando para Aproveitar.

    ![Escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![O botão de provisionamento Salvar](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial dos usuários ou grupos que você está provisionando. A sincronização inicial leva mais tempo para ser realizado do que as posteriores. As sincronizações ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD esteja sendo executado. Para monitorar o progresso, vá para a seção Detalhes de **sincronização.** Você também pode seguir links para um relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Harness.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatório sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
