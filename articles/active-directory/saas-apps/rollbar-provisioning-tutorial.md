---
title: 'Tutorial: Configure rollbar para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para rollbar.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d737aa16-8ab4-4c0c-a68b-2911623b41eb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 27a26a0c8378f34794afd87cf11b6bb878f7b53c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248417"
---
# <a name="tutorial-configure-rollbar-for-automatic-user-provisioning"></a>Tutorial: Configure rollbar para provisionamento automático do usuário

Este tutorial descreve as etapas que você precisa executar tanto no Rollbar quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático do usuário. Quando configurado, o Azure AD fornece automaticamente e desprovisiona usuários e grupos para [Rollbar](https://rollbar.com/pricing/) usando o serviço de provisionamento Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Recursos suportados
> [!div class="checklist"]
> * Criar usuários na Barra de Rolagem
> * Remova os usuários em Rollbar quando eles não precisarem mais de acesso
> * Mantenha os atributos do usuário sincronizados entre a Azure AD e a Rollbar
> * Grupos de provisão e membros de grupos em Rollbar
> * [Único sinal para](https://docs.microsoft.com/azure/active-directory/saas-apps/rollbar-tutorial) Rollbar (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos na nuvem, proprietário de aplicativos ou administrador global). 
* [Um inquilino rollbar](https://rollbar.com/pricing/) que tem um plano corporativo.
* Uma conta de usuário em Rollbar com permissões de administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje sua implantação de provisionamento
1. Saiba [como funciona o serviço de provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determine quem estará no [escopo para provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais dados [a mapear entre o Azure AD e o Rollbar](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-rollbar-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configure rollbar para suportar provisionamento com a Azure AD

Antes de configurar o Rollbar para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento SCIM no Rollbar.

1. Faça login no seu [console de admin Rollbar .](https://rollbar.com/login/) Clique em **Configurações da conta**.

    ![Rollbar Admin Console](media/rollbar-provisioning-tutorial/image00.png)

2. Navegue até o **seu Provedor de Identidade do Inquilino da Barra De Arquivo >**.

    ![Provedor de identidade Rollbar](media/rollbar-provisioning-tutorial/idp.png)

3. Desça até **opções de provisionamento**. Copie o token de acesso. Esse valor será inserido no campo **Token Secreto** na guia de provisionamento do aplicativo Rollbar no portal Azure. Selecione a **caixa de seleção de provisionamento do usuário e da equipe** e clique em **Salvar**.

    ![Token de acesso rollbar](media/rollbar-provisioning-tutorial/token.png)


## <a name="step-3-add-rollbar-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar rollbar na galeria de aplicativos azure AD

Adicione rollbar da galeria de aplicativos Azure AD para começar a gerenciar o provisionamento para rollbar. Se você tiver configurado anteriormente rollbar para SSO, você pode usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo na galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo para provisionamento 

O serviço de provisionamento Azure AD permite que você escopo que será provisionado com base na atribuição para o aplicativo e ou com base em atributos do usuário /grupo. Se você optar por escopo de quem será provisionado para o seu aplicativo com base na atribuição, você pode usar as [seguintes etapas](../manage-apps/assign-user-or-group-access-portal.md) para atribuir usuários e grupos ao aplicativo. Se você optar por escopo que será provisionado com base apenas em atributos do usuário ou grupo, você pode usar um filtro de escopo como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao Rollbar, você deve selecionar uma função diferente **do Default Access**. Os usuários com a função Default Access são excluídos do provisionamento e serão marcados como não efetivamente habilitados nos registros de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você pode [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de ser lançado para todos. Quando o escopo para provisionamento é definido para usuários e grupos atribuídos, você pode controlá-lo atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-rollbar"></a>Etapa 5. Configure o provisionamento automático do usuário para rollbar 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-rollbar-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para Rollbar no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Rollbar**.

    ![O link do Rollbar na Lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de **Admin,** insira o valor do token de acesso recuperado anteriormente no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar à Rollbar. Se a conexão falhar, certifique-se de que sua conta Rollbar tenha permissões de administração e tente novamente.

    ![Provisionamento](./media/rollbar-provisioning-tutorial/admin.png)

6. No campo **E-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **Notificação enviar um e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para rollbar**.

9. Revise os atributos do usuário sincronizados do Azure AD para o Rollbar na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Rollbar para operações de atualização. Se você optar por alterar o [atributo de destino correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)você precisará garantir que a API Rollbar suporte a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |externalId|String|
   |ativo|Boolean|
   |name.familyName|String|
   |name.givenName|String|
   |e-mails[tipo eq "trabalho"]|String|

10. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para Rollbar**.

11. Revise os atributos de grupo sincronizados do Azure AD para rollbar na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder aos grupos no Rollbar para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para Rollbar, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para Rollbar escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os usuários e grupos definidos no **Escopo** na seção **Configurações.** O ciclo inicial leva mais tempo para ser executado do que os ciclos subseqüentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD esteja sendo executado. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

1. Use os [registros de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e quão perto está da conclusão
3. Se a configuração de provisionamento parecer estar em um estado insalubre, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
