---
title: 'Tutorial: configurar o treinamento de reconhecimento de segurança do Webroot para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para treinamento de reconhecimento de segurança do Webroot.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: e229fdd44bb6d634d83ddfaf1173c02b65336537
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519241"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Tutorial: configurar o treinamento de reconhecimento de segurança do Webroot para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no treinamento de reconhecimento de segurança do Webroot quanto no Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [treinamento de reconhecimento de segurança do Webroot](https://www.webroot.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no treinamento de reconhecimento de segurança do Webroot
> * Remova os usuários no treinamento de reconhecimento de segurança do Webroot quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o treinamento de reconhecimento de segurança do Azure AD e do Webroot
> * Provisionar grupos e associações de grupo no treinamento de reconhecimento de segurança do Webroot

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md). 
* Uma conta de usuário no Azure AD com [permissão](../users-groups-roles/directory-assign-admin-roles.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global).
* Um console de provedor de serviços gerenciado com treinamento de reconhecimento de segurança do Webroot habilitado para pelo menos um de seus sites.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapear entre o treinamento de reconhecimento de segurança do Azure AD e o Webroot](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o treinamento de reconhecimento de segurança do Webroot para dar suporte ao provisionamento com o Azure AD

### <a name="obtain-a-secret-token"></a>Obter um token secreto

Para conectar seu site ao Azure AD, será necessário obter um **token secreto** para esse site no console de gerenciamento do Webroot.

1. Entre no [console de gerenciamento do Webroot](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. Na guia **sites** , clique no ícone de engrenagem na coluna treinamento de reconhecimento de segurança para o site que você deseja conectar ao Azure AD.

    ![Ícone de engrenagem](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Clique no botão para **Configurar a integração do Azure ad**.

    ![Configurar a integração do Azure AD](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Copie e salve o **token secreto**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo de treinamento de reconhecimento de segurança do Webroot na portal do Azure.

5. Clique em **Concluído**.

    ![Copiar token secreto](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar treinamento de reconhecimento de segurança do Webroot da Galeria de aplicativos do Azure AD

Adicione treinamento de reconhecimento de segurança do Webroot da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para o treinamento de reconhecimento de segurança do Webroot. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao treinamento de reconhecimento de segurança do Webroot, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>Etapa 5. Configurar o provisionamento automático de usuário para o treinamento de reconhecimento de segurança do Webroot 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para treinamento de reconhecimento de segurança do Webroot no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **treinamento de reconhecimento de segurança do Webroot**.

    ![O link de treinamento de reconhecimento de segurança do Webroot na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://awarenessapi.webrootanywhere.com/api/v2/scim` a **URL de locatário**. Insira o valor do token secreto recuperado anteriormente em **Token Secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao treinamento de reconhecimento de segurança do Webroot. Se a conexão falhar, verifique se sua conta de treinamento de reconhecimento de segurança do Webroot tem permissões de administrador e tente novamente.

    ![Captura de tela mostra a caixa de diálogo credenciais de administrador, em que é possível inserir seu locatário U R L e token secreto.](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Provisionar usuários do Azure Active Directory**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para treinamento de reconhecimento de segurança do Webroot na seção de **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no treinamento de reconhecimento de segurança do Webroot para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), será necessário garantir que a API de treinamento de reconhecimento de segurança do Webroot dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |externalId|String|&check;|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|

10. Na seção **mapeamentos** , selecione **provisionar grupos de Azure Active Directory**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para treinamento de reconhecimento de segurança do Webroot na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no treinamento de reconhecimento de segurança do Webroot para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|Com suporte para filtragem|
      |---|---|---|
      |displayName|String|&check;|
      |membros|Referência|
      |externalId|String|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para treinamento de reconhecimento de segurança do Webroot, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o treinamento de reconhecimento de segurança do Webroot escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)