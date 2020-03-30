---
title: 'Tutorial: Configure zoom para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuários do Azure AD para zoom.
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
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 94c261da0c935cb7a41dde768069099b4e5ed251
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384068"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: Configure zoom para provisionamento automático do usuário

Este tutorial descreve as etapas que você precisa executar tanto no Zoom quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático do usuário. Quando configurado, o Azure AD fornece automaticamente e desprovisiona usuários e grupos para [zoom](https://zoom.us/pricing/) usando o serviço de provisionamento Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Recursos suportados
> [!div class="checklist"]
> * Criar usuários no Zoom
> * Remova os usuários no Zoom quando eles não precisarem mais de acesso
> * Mantenha os atributos do usuário sincronizados entre o Azure AD e o Zoom
> * [Único login no](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) Zoom (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos na nuvem, proprietário de aplicativos ou administrador global). 
* [Um inquilino zoom](https://zoom.us/pricing).
* Uma conta de usuário no Zoom com permissões de administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje sua implantação de provisionamento
1. Saiba [como funciona o serviço de provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determine quem estará no [escopo para provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais dados [a mapear entre o Azure AD e o Zoom](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configure zoom para suportar provisionamento com a Azure AD

1. Faça login no seu [zoom admin console](https://zoom.us/signin). Navegue até **o > Zoom Avançado para Desenvolvedores** no painel de navegação esquerdo.

    ![Integrações de Zoom](media/zoom-provisioning-tutorial/zoom01.png)

2. Navegue para **Gerenciar** no canto superior direito da página. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom02.png)

3. Navegue até o aplicativo Azure AD criado. 
    
    ![Zoom App](media/zoom-provisioning-tutorial/zoom03.png)

4. Selecione **Credenciais de aplicativo** no painel de navegação à esquerda.

    ![Zoom App](media/zoom-provisioning-tutorial/zoom04.png)

5. Copie e salve o **Token JWT**. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Zoom no portal Azure. Se você precisar de um novo token sem expiração, você precisará reconfigurar o tempo de expiração que gerará automaticamente um novo token. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar zoom na galeria de aplicativos do Azure AD

Adicione zoom da galeria de aplicativos Azure AD para começar a gerenciar o provisionamento para zoom. Se você tiver configurado anteriormente zoom para SSO, você pode usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo na galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo para provisionamento 

O serviço de provisionamento Azure AD permite que você escopo que será provisionado com base na atribuição para o aplicativo e ou com base em atributos do usuário /grupo. Se você optar por escopo de quem será provisionado para o seu aplicativo com base na atribuição, você pode usar as [seguintes etapas](../manage-apps/assign-user-or-group-access-portal.md) para atribuir usuários e grupos ao aplicativo. Se você optar por escopo que será provisionado com base apenas em atributos do usuário ou grupo, você pode usar um filtro de escopo como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao Zoom, você deve selecionar uma função diferente **do Acesso Padrão**. Os usuários com a função Default Access são excluídos do provisionamento e serão marcados como não efetivamente habilitados nos registros de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você pode [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de ser lançado para todos. Quando o escopo para provisionamento é definido para usuários e grupos atribuídos, você pode controlá-lo atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Etapa 5. Configure o provisionamento automático do usuário para zoom 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para zoom no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zoom**.

    ![O link do Zoom na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** insira `https://api.zoom.us/scim` **url de inquilino**. Insira o valor **do Token JWT** recuperado anteriormente no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Zoom. Se a conexão falhar, certifique-se de que sua conta Zoom tenha permissões de administração e tente novamente.

    ![Provisionamento de zoom](./media/zoom-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **Notificação enviar um e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para ampliar**.

9. Revise os atributos do usuário sincronizados do Azure AD para o Zoom na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Zoom para operações de atualização. Se você optar por alterar o [atributo de destino correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)você precisará garantir que a API zoom suporte aos usuários filtrantes com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |ativo|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |e-mails[tipo eq "trabalho"]|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento Azure AD para Zoom, altere o **status de provisionamento** para **ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar para zoom escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os usuários e grupos definidos no **Escopo** na seção **Configurações.** O ciclo inicial leva mais tempo para ser executado do que os ciclos subseqüentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD esteja sendo executado. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

1. Use os [registros de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e quão perto está da conclusão
3. Se a configuração de provisionamento parecer estar em um estado insalubre, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="connector-limitations"></a>Limitações do conector
* O Zoom só permite um máximo de 9.999 usuários básicos hoje.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
