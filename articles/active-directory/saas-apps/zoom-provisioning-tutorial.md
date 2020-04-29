---
title: 'Tutorial: configurar o zoom para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o zoom.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80384068"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: configurar o zoom para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no zoom e Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [aplicar zoom](https://zoom.us/pricing/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários em zoom
> * Remover usuários com zoom quando não exigirem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o zoom
> * [Logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) para zoom (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global). 
* [Um locatário de zoom](https://zoom.us/pricing).
* Uma conta de usuário em zoom com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planejar sua implantação de provisionamento
1. Saiba mais sobre [como o serviço de provisionamento funciona](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapeados entre o Azure AD e o zoom](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o zoom para oferecer suporte ao provisionamento com o Azure AD

1. Entre no console do [administrador de zoom](https://zoom.us/signin). Navegue até **Advanced > zoom para desenvolvedores** no painel de navegação esquerdo.

    ![Ampliar integrações](media/zoom-provisioning-tutorial/zoom01.png)

2. Navegue até **gerenciar** no canto superior direito da página. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom02.png)

3. Navegue até seu aplicativo do Azure AD criado. 
    
    ![Aplicativo de zoom](media/zoom-provisioning-tutorial/zoom03.png)

4. Selecione **credenciais do aplicativo** no painel de navegação à esquerda.

    ![Aplicativo de zoom](media/zoom-provisioning-tutorial/zoom04.png)

5. Copie e salve o **token JWT**. Esse valor será inserido no campo **token secreto** na guia provisionamento do aplicativo de Zoom na portal do Azure. Se você precisar de um novo token sem expiração, será necessário reconfigurar o tempo de expiração que gerará automaticamente um novo token. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar zoom da Galeria de aplicativos do Azure AD

Adicione zoom da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no zoom. Se você tiver configurado anteriormente o zoom para SSO, poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo da Galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se você optar por definir o escopo que será provisionado em seu aplicativo com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você escolher o escopo que será provisionado com base apenas em atributos do usuário ou grupo, você poderá usar um filtro de escopo conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao zoom, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido como todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Etapa 5. Configurar o provisionamento automático de usuário para aplicar zoom 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para zoom no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zoom**.

    ![O link do Zoom na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://api.zoom.us/scim` na **URL do locatário**. Insira o valor do **token JWT** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao zoom. Se a conexão falhar, verifique se sua conta de zoom tem permissões de administrador e tente novamente.

    ![Provisionamento de zoom](./media/zoom-provisioning-tutorial/provisioning.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para aplicar zoom**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para ampliar a seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário em zoom para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API de zoom ofereça suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|Cadeia de caracteres|
   |ativo|Booliano|
   |name.givenName|Cadeia de caracteres|
   |name.familyName|Cadeia de caracteres|
   |emails [tipo EQ "trabalho"]|Cadeia de caracteres|
   |urn: IETF: params: SCIM: esquemas: extensão: Enterprise: 2.0: User: Department|Cadeia de caracteres|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para zoom, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para aplicar zoom escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia o ciclo de sincronização inicial de todos os usuários e grupos definidos no **escopo** na seção **configurações** . O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e como fechá-lo para conclusão
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os Estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Limitações do conector
* O zoom só permite um máximo de 9.999 usuários básicos de hoje.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
