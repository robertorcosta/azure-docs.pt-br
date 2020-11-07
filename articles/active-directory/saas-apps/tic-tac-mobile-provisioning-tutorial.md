---
title: 'Tutorial: configurar o Tic-Tac Mobile para o provisionamento automático de usuário com Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para Tic-Tac dispositivos móveis.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 99fe2f8356b53b5d8e459dbf62534ddb4f0019b2
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357259"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Tutorial: configurar o Tic-Tac Mobile para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar em Tic-Tac dispositivos móveis e Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuários. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [jogo Mobile](https://www.tictacmobile.com/) usando o serviço de provisionamento do Azure AD. Para obter informações sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS (software como serviço) com o Azure ad](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Funcionalidades com suporte

> [!div class="checklist"]
> * Crie usuários no Tic-Tac Mobile.
> * Remova os usuários no Tic-Tac Mobile quando eles não exigem mais acesso.
> * Mantenha os atributos de usuário sincronizados entre o Azure AD e o Tic-Tac Mobile.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento. Os exemplos são administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global.
* Uma conta [móvel jogo-TAC](https://www.tictacmobile.com/) com uma função de superadministrador.


## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento

1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Determine quais dados [mapeados entre o Azure AD e o Tic-Tac Mobile](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar Tic-Tac Mobile para dar suporte ao provisionamento com o Azure AD

Contate support@tictacmobile.com para obter a **URL do locatário** e o **token secreto**. Você deve ter uma função de superadministrador no Tic-Tac Mobile para receber um token. O token será inserido na caixa **token secreto** na guia **provisionamento** do seu aplicativo Tic-Tac Mobile na portal do Azure.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar Tic-Tac Mobile da Galeria de aplicativos do Azure AD

Adicione Tic-Tac Mobile da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para Tic-Tac Mobile. Se você tiver configurado anteriormente Tic-Tac Mobile para logon único, poderá usar o mesmo aplicativo. Quando você testar a integração inicialmente, crie um aplicativo separado. Para saber mais sobre como adicionar um aplicativo da galeria, confira [provisionamento de aplicativo baseado em atributo com filtros de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento

Com o serviço de provisionamento do Azure AD, você pode definir o escopo que será provisionado com base na atribuição para o aplicativo ou com base nos atributos do usuário ou grupo. Se você optar por definir o escopo que será provisionado em seu aplicativo com base na atribuição, siga as etapas em [gerenciar atribuição de usuário para um aplicativo no Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) para atribuir usuários e grupos ao aplicativo. Se você optar pelo escopo que será provisionado com base apenas em atributos do usuário ou grupo, use um filtro de escopo conforme descrito em provisionamento de [aplicativo baseado em atributo com filtros de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* Ao atribuir usuários e grupos ao Tic-Tac Mobile, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar mais funções.
* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode manter o controle atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido como todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Etapa 5. Configurar o provisionamento automático de usuário para Tic-Tac dispositivos móveis

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no TestApp com base em atribuições de usuário ou de grupo no Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Tic-Tac Mobile no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Captura de tela que mostra o painel aplicativos empresariais.](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **jogo-TAC Mobile**.

    ![Captura de tela que mostra o link Tic-Tac Mobile na lista de aplicativos.](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Captura de tela que mostra a guia provisionamento.](common/provisioning.png)

1. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela que mostra a opção de provisionamento de guia automática.](common/provisioning-automatic.png)

1. Na seção **credenciais de administrador** , insira seu Tic-Tac **URL de locatário** móvel e **token secreto**. Selecione **testar conexão** para garantir que o Azure ad possa se conectar ao Tic-Tac Mobile. Se a conexão falhar, verifique se sua conta do Tic-Tac Mobile tem permissões de administrador e tente novamente.

    ![Captura de tela que mostra a caixa token secreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Captura de tela que mostra a caixa email de notificação.](common/provisioning-notification-email.png)

1. Selecione **Salvar**.

1. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Tic-Tac Mobile**.

1. Examine os atributos de usuário que são sincronizados do Azure AD para Tic-Tac Mobile na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Tic-Tac Mobile para operações de atualização. Se você alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), deverá garantir que o Tic-Tac API móvel dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |título|String|
   |emails [tipo EQ "trabalho"]. valor|String|
   |preferredLanguage|String|
   |externalId|String|
   |userType|String|
   |localidade|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

1. Para configurar filtros de escopo, consulte as instruções no [tutorial filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD para Tic-Tac Mobile, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Captura de tela que mostra o status de provisionamento alternado em.](common/provisioning-toggle-on.png)

1. Defina os usuários ou grupos que você deseja provisionar para Tic-Tac dispositivos móveis selecionando os valores desejados em **escopo** na seção **configurações** .

    ![Captura de tela que mostra o escopo de provisionamento.](common/provisioning-scope.png)

1. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Captura de tela que mostra como salvar a configuração de provisionamento.](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação

Depois de configurar o provisionamento, use os recursos a seguir para monitorar sua implantação.

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não.
1. Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir.
1. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Para saber mais sobre os Estados de quarentena, confira [provisionamento de aplicativos em status de quarentena](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
