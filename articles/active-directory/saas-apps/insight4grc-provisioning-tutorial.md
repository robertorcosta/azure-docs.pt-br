---
title: 'Tutorial: configurar o Insight4GRC para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Insight4GRC.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 02/04/2020
ms.author: Zhchia
ms.openlocfilehash: 86927a525fbfcb6a252d8e3d214733a0f1e83ed1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550366"
---
# <a name="tutorial-configure-insight4grc-for-automatic-user-provisioning"></a>Tutorial: configurar o Insight4GRC para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no Insight4GRC quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [Insight4GRC](https://www.rsmuk.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Insight4GRC
> * Remover usuários no Insight4GRC quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Insight4GRC
> * Provisionar grupos e associações de grupo no Insight4GRC
> * [Logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial) no Insight4GRC (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global). 
* Uma conta de usuário no Insight4GRC com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapeados entre o Azure AD e o Insight4GRC](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-insight4grc-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Insight4GRC para dar suporte ao provisionamento com o Azure AD

Antes de configurar o Insight4GRC para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no Insight4GRC.

1. Para obter o token de portador, o cliente final precisa entrar em contato com a [equipe de suporte](mailto:support.ss@rsmuk.com).
2. Para obter a URL do ponto de extremidade SCIM, você precisará ter o nome de domínio do Insight4GRC pronto, pois ele será usado para construir sua URL de ponto de extremidade SCIM. Você pode recuperar o nome de domínio do Insight4GRC como parte da compra inicial do software com o Insight4GRC.

## <a name="step-3-add-insight4grc-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Insight4GRC da Galeria de aplicativos do Azure AD

Adicione o Insight4GRC da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no Insight4GRC. Se você tiver configurado anteriormente o Insight4GRC para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao Insight4GRC, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-insight4grc"></a>Etapa 5. Configurar o provisionamento automático de usuário para o Insight4GRC 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Insight4GRC no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Insight4GRC**.

    ![O link do Insight4GRC na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do ponto de extremidade scim na **URL do locatário**. A URL de ponto deve estar no formato `https://<Insight4GRC Domain Name>.insight4grc.com/public/api/scim/v2 ` em que **Insight4GRC nome de domínio** é o valor recuperado nas etapas anteriores. Insira o valor do token de portador recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Insight4GRC. Se a conexão falhar, verifique se sua conta do Insight4GRC tem permissões de administrador e tente novamente.

    ![provisionamento](./media/insight4grc-provisioning-tutorial/provisioning.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Insight4GRC**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Insight4GRC na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Insight4GRC para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API Insight4GRC dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |externalId|String|
   |ativo|Boolean|
   |título|String|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|
   |phoneNumbers[type eq "work"].value|String|

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Insight4GRC**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para o Insight4GRC na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Insight4GRC para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Insight4GRC, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o Insight4GRC escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

* Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não.
* Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir.
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
