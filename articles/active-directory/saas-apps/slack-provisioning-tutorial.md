---
title: 'Tutorial: provisionamento de usuário para a margem de atraso-Azure AD'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Slack.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b7fa5aea835329be8f65a3bb1775ba5b0d97d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389846"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Tutorial: Configurar Slack para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Slack e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Slack. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários na margem de atraso
> * Remova os usuários na margem de atraso quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e a margem de atraso
> * Provisionar grupos e associações de grupo na margem de atraso
> * O [logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial) para a margem de atraso (recomendado)


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* [Um locatário do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global).
* Um locatário de margem de atraso com o [plano de adição](https://aadsyncfabric.slack.com/pricing) ou melhor habilitado.
* Uma conta de usuário na margem de atraso com permissões de administrador de equipe.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapeados entre o Azure AD e a margem de atraso](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Etapa 2. Adicionar margem de atraso da Galeria de aplicativos do Azure AD

Adicione a margem de atraso da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para a margem de atraso. Se você tiver configurado anteriormente a margem de atraso para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Etapa 3. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos à margem de atraso, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Etapa 4. Configurar o provisionamento automático de usuário para a margem de atraso 

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do Slack e pela configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas no Slack com base na atribuição de usuário e de grupo do Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Slack no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Slack**.

    ![O link do Slack na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Isso abre uma caixa de diálogo de autorização do Slack em uma nova janela do navegador.

    ![Autorização](media/slack-provisioning-tutorial/authorization.png)


6. Na nova janela, entre no Slack usando sua conta de Administrador de Equipe. Na caixa de diálogo de autorização resultante, selecione a equipe do Slack para a qual você deseja habilitar o provisionamento e, em seguida, selecione **Autorizar**. Depois de concluído, retorne ao Portal do Azure para concluir a configuração de provisionamento.

    ![Caixa de diálogo Autorização](./media/slack-provisioning-tutorial/slackauthorize.png)

7. No Portal do Azure, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo Slack. Se a conexão falhar, verifique se a sua conta do Slack tem permissões de Administrador de Equipe e repita a etapa "Autorizar".

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Na seção Mapeamentos, selecione **Sincronizar usuários do Azure Active Directory com o Slack**.

11. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD para o Slack. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Slack para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |ativo|Boolean|
   |externalId|String|
   |displayName|String|
   |name.familyName|String|
   |name.givenName|String|
   |título|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |Apelido|String|
   |endereços [tipo EQ "não tipado"]. streetAddress|String|
   |endereços [tipo EQ "não tipado"]. localidade|String|
   |endereços [tipo EQ "não tipado"]. região|String|
   |endereços [tipo EQ "não tipado"]. postalCode|String|
   |endereços [tipo EQ "não tipado"]. país|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |funções [EQ principal "true"]. valor|String|
   |localidade|String|
   |nome. honorificPrefix|String|
   |fotos [tipo EQ "foto"]. valor|String|
   |profileUrl|String|
   |timezone|String|
   |userType|String|
   |urn: SCIM: esquemas: extensão: Enterprise: 1.0. Department|String|
   |urn: SCIM: esquemas: extensão: Enterprise: 1.0. Manager|Referência|
   |urn: SCIM: esquemas: extensão: Enterprise: 1.0. employeeNumber|String|
   |urn: SCIM: esquemas: extensão: Enterprise: 1.0. costCenter|String|
   |urn: SCIM: esquemas: extensão: Enterprise: 1.0. Organization|String|
   |urn: SCIM: esquemas: extensão: Enterprise: 1.0. Division|String|

12. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory grupos à margem de atraso**.

13. Na seção **Mapeamentos de Atributo**, revise os atributos de grupo que serão sincronizados do Azure AD para o Slack. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder os grupos no Slack para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |membros|Referência|

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o Slack, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

16. Defina os usuários e/ou grupos que você gostaria de provisionar para a margem de atraso escolhendo os valores desejados em **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-5-monitor-your-deployment"></a>Etapa 5. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Limitações do conector

* Ao configurar o atributo **displayName** do Slack, esteja ciente dos seguintes comportamentos:

  * Os valores não são totalmente exclusivos (por exemplo, 2 usuários podem ter o mesmo nome de exibição)

  * Suporta caracteres não ingleses, espaços e letras maiúsculas. 
  
  * As pontuações permitidas são pontos, sublinhados, hifens, apóstrofos, colchetes (por exemplo, ** ( [ { } ] ) **) e separadores (por exemplo, **, /; **).
  
  * Apenas atualiza se essas duas configurações estiverem configuradas no local de trabalho / organização do Slack - **A sincronização de perfil está ativada** e **Os usuários não podem alterar seu nome de exibição**.
  
* O atributo **userName** do Slack precisa ter menos de 21 caracteres e ter um valor exclusivo.

* A margem de atraso só permite correspondência com os atributos **username** e **email**.  

## <a name="change-log"></a>Log de alterações

* 06/16/2020-atributo DisplayName modificado para ser atualizado somente durante a nova criação do usuário.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)