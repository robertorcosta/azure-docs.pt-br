---
title: 'Tutorial: Configure foodee para provisionamento automático do usuário usando o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Foodee.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057798"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Tutorial: Configure Foodee para provisionamento automático do usuário

Este artigo mostra como configurar o Azure Active Directory (Azure AD) no Foodee e no Azure AD para provisionar ou desprovisionar automaticamente usuários ou grupos para o Foodee.

> [!NOTE]
> O artigo descreve um conector que é construído em cima do serviço de provisionamento de usuários Azure AD. Para saber o que esse serviço faz e como ele funciona e obter respostas para perguntas frequentes, consulte [Automate provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre o recurso de termos de uso do Azure para recursos de visualização, acesse [Termos de Uso Suplementar escaneados para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que você conheceu os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino foodee](https://www.food.ee/about/)
* Uma conta de usuário em Foodee com permissões de administração

## <a name="assign-users-to-foodee"></a>Atribuir usuários ao Foodee 

O Azure AD usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários ou grupos no Azure AD precisam ter acesso ao Foodee. Depois de fazer essa determinação, você pode atribuir esses usuários ou grupos ao Foodee seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo corporativo](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Dicas importantes para atribuir usuários ao Foodee 

Ao atribuir aos usuários as seguintes dicas:

* Recomendamos que você atribua apenas um único usuário Azure AD ao Foodee para testar a configuração do provisionamento automático do usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Ao atribuir um usuário ao Foodee, selecione qualquer função específica de aplicativo válida, se estiver disponível, no painel **Atribuição.** Os usuários que possuem a função *Default Access* são excluídos do provisionamento.

## <a name="set-up-foodee-for-provisioning"></a>Configurar Foodee para provisionamento

Antes de configurar o Foodee para provisionamento automático do usuário usando o Azure AD, você precisa habilitar o provisionamento do System for Cross-domain Identity Management (SCIM) no Foodee.

1. Faça login no [Foodee](https://www.food.ee/login/)e selecione seu id de inquilino.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. Em **portal Enterprise,** selecione **Single Sign On**.

    ![O menu de painel esquerdo do Foodee Enterprise Portal](media/Foodee-provisioning-tutorial/scim.png)

1. Copie o valor na **caixa API Token** para uso posterior. Você insere-o na caixa **De Token Secreto** na guia **Provisionamento** do seu aplicativo Foodee no portal Azure.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Adicione Foodee da galeria

Para configurar o Foodee para provisionamento automático do usuário usando o Azure AD, você precisa adicionar o Foodee da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Foodee na galeria de aplicativos do Azure AD, faça o seguinte:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![O comando Azure Active Directory](common/select-azuread.png)

1. Selecione **aplicativos Corporativos** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, **digite Foodee**, **selecione Foodee** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar o aplicativo.

    ![Foodee na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Configure o provisionamento automático do usuário para foodee 

Nesta seção, você configura o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários ou grupos no Foodee com base em atribuições de usuários ou grupos no Azure AD.

> [!TIP]
> Você também pode habilitar o login único baseado em SAML para foodee seguindo as instruções no [tutorial de signon-on único foodee](Foodee-tutorial.md). Você pode configurar um único login independente do provisionamento automático do usuário, embora esses dois recursos se complementem.

Configure o provisionamento automático do usuário para Foodee no Azure AD fazendo o seguinte:

1. No [portal Azure,](https://portal.azure.com)selecione **Aplicativos Corporativos** > **Todos os aplicativos**.

    ![Painel Aplicativos empresariais](common/enterprise-applications.png)

1. Na lista **De aplicações,** **selecione Foodee**.

    ![O link Foodee na lista de aplicativos](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

1. Na lista de isto do modo de **provisionamento,** selecione **Automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

1. Em **Credenciais de Admin,** faça o seguinte:

   a. Na caixa URL do **Inquilino,** digite o valor **https:\//concierge.food.ee/scim/v2** que você recuperou anteriormente.

   b. Na caixa **De Token Secreto,** digite o valor de Token da **API** que você recuperou anteriormente.
   
   c. Para garantir que o Azure AD possa se conectar ao Foodee, selecione **Conexão de teste**. Se a conexão falhar, certifique-se de que sua conta Foodee tenha permissões de administrador e tente novamente.

    ![O link Conexão de teste](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa **De e-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e, em seguida, selecione a **Notificação enviar um e-mail quando ocorrer uma falha** na caixa de seleção.

    ![A caixa de texto de e-mail de notificação](common/provisioning-notification-email.png)

1. Selecione **Salvar**.

1. Em **Mapeamentos,** selecione **Sincronizar usuários de diretórioativo ativo do Azure para foodee**.

    ![Mapeamentos de usuários foodee](media/Foodee-provisioning-tutorial/usermapping.png)

1. Em **Attribute Mappings**, revise os atributos do usuário sincronizados do Azure AD para o Foodee. Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de *usuário* no Foodee para operações de atualização. 

    ![Mapeamentos de usuários foodee](media/Foodee-provisioning-tutorial/userattribute.png)

1. Para efetuar as alterações, **selecione Salvar**.
1. Em **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para Foodee**.

    ![Mapeamentos de usuários foodee](media/Foodee-provisioning-tutorial/groupmapping.png)

1. Em **Attribute Mappings**, revise os atributos do usuário sincronizados do Azure AD para o Foodee. Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de *grupo* no Foodee para operações de atualização.

    ![Mapeamentos de usuários foodee](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Para efetuar as alterações, **selecione Salvar**.
1. Configure os filtros de escopo. Para saber como, consulte as instruções no [tutorial do filtro Scoping](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento Azure AD para Foodee, na seção **Configurações,** altere o **Status de Provisionamento** para **On**.

    ![O interruptor de status de provisionamento](common/provisioning-toggle-on.png)

1. Em **Configurações,** na lista de parada **escopo,** defina os usuários ou grupos que você deseja prospecção ao Foodee.

    ![A lista de isto de provisionamento escopo](common/provisioning-scope.png)

1. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![O botão Salvar configuração de provisionamento](common/provisioning-configuration-save.png)

A operação anterior inicia a sincronização inicial dos usuários ou grupos definidos na lista de parada **do Escopo.** A sincronização inicial leva mais tempo para ser realizado do que as sincronizações subseqüentes. Para obter mais informações, consulte [quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **Status atual** para monitorar o progresso e seguir links para o seu relatório de atividade de provisionamento. O relatório descreve todas as ações que são realizadas pelo serviço de provisionamento Azure AD na Foodee. Para obter mais informações, consulte [Verifique o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
