---
title: 'Tutorial: Configurar o G Suite para provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e descontinuar automaticamente as contas de usuário do Azure AD para o G Suite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 3f2f62fe158b946e00c7f81d0cb7eeb0d8f09437
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331119"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: configurar o G Suite para provisionamento automático de usuários

Este tutorial descreve as etapas que você precisa executar no G Suite e no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [G Suite](https://gsuite.google.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> O conector do G Suite foi atualizado recentemente em outubro de 2019. As alterações feitas no conector do G Suite incluem:
>
> * Adição de suporte para atributos adicionais de usuário e grupo do G Suite.
> * Atualizado os nomes de atributo de destino do G Suite para corresponder ao que está definido [aqui](https://developers.google.com/admin-sdk/directory).
> * Mapeamentos de atributo padrão atualizados.

## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no G Suite
> * Remover usuários do G Suite quando eles não precisam mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o G Suite
> * Provisionar grupos e associações de grupo no G Suite
> * [Logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial) no G Suite (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global). 
* [Um locatário do G Suite](https://gsuite.google.com/pricing.html)
* Uma conta de usuário em um G Suite com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapear entre o Azure AD e o G Suite](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o G Suite para dar suporte ao provisionamento com o Azure AD

Antes de configurar o G Suite para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no G Suite.

1. Entre no console de [Administração do G Suite](https://admin.google.com/) com sua conta de administrador e selecione **segurança**. Se você não vê o link, ele pode estar oculto sob o menu **Mais Controles**, na parte inferior da tela.

    ![Segurança do G Suite](./media/google-apps-provisioning-tutorial/gapps-security.png)

2. Na página **Segurança**, selecione **Referência de API**.

    ![API do G Suite](./media/google-apps-provisioning-tutorial/gapps-api.png)

3. Selecione **Habilitar acesso à API**.

    ![API do G Suite habilitada](./media/google-apps-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Para cada usuário que você pretende provisionar para o G Suite, seu nome de usuário no Azure AD **deve** estar vinculado a um domínio personalizado. Por exemplo, nomes de usuários do tipo bob@contoso.onmicrosoft.com não são aceitos pelo G Suite. Por outro lado, bob@contoso.com é aceito. Você pode alterar o domínio de um usuário existente seguindo as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

4. Depois de adicionar e verificar os domínios personalizados desejados com o Azure AD, você deve verificá-los novamente com o G Suite. Para verificar os domínios no G Suite, consulte as seguintes etapas:

    a. No [console de administração do G Suite](https://admin.google.com/), selecione **domínios**.

    ![Domínios do G Suite](./media/google-apps-provisioning-tutorial/gapps-domains.png)

    b. Selecione **Adicionar um domínio ou um alias de domínio**.

    ![G Suite-Adicionar domínio](./media/google-apps-provisioning-tutorial/gapps-add-domain.png)

    c. Selecione **Adicionar outro domínio** e digite o nome do domínio que você deseja adicionar.

    ![G Suite-adicionar outro](./media/google-apps-provisioning-tutorial/gapps-add-another.png)

    d. Selecione **Continuar e confirmar a propriedade do domínio**. Em seguida, siga as etapas para verificar se você possui o nome de domínio. Para obter instruções abrangentes sobre como verificar seu domínio com o Google, consulte [verificar a propriedade do site](https://support.google.com/webmasters/answer/35179).

    e. Repita as etapas anteriores para todos os domínios adicionais que você pretende adicionar ao G Suite.

5. Em seguida, determine qual conta de administrador você deseja usar para gerenciar o provisionamento de usuário no G Suite. Navegue até **funções de administrador**.

    ![Administrador do G Suite](./media/google-apps-provisioning-tutorial/gapps-admin.png)

6. Para a **função de administrador** dessa conta, edite os **privilégios** para essa função. Verifique se você habilitou todos os **Privilégios de API de Administrador**, de modo que essa conta possa ser usada para o provisionamento.

    ![Privilégios de administrador do G Suite](./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o G Suite da Galeria de aplicativos do Azure AD

Adicione o G Suite da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para o G Suite. Se você tiver configurado anteriormente o G Suite para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao G Suite, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Etapa 5. Configurar o provisionamento automático de usuário para o G Suite 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade da API do diretório do G Suite, consulte [API de diretório](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o G Suite no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**. Os usuários precisarão fazer logon no portal.azure.com e não poderão usar o aad.portal.azure.com

    ![Folha de aplicativos empresariais](./media/google-apps-provisioning-tutorial/enterprise-applications.png)

    ![Folha Todos os aplicativos](./media/google-apps-provisioning-tutorial/all-applications.png)

2. Na lista de aplicativos, selecione **G Suite**.

    ![O link do G Suite na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**. Clique em **Introdução**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

      ![Folha de introdução](./media/google-apps-provisioning-tutorial/get-started.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , clique em **autorizar**. Você será redirecionado para uma caixa de diálogo de autorização do Google em uma nova janela do navegador.

      ![Autorização do G Suite](./media/google-apps-provisioning-tutorial/authorize-1.png)

6. Confirme que você deseja conceder permissões do Azure AD para fazer alterações no seu locatário do G Suite. Selecione **Aceitar**.

     ![Autenticação de locatário G Suite](./media/google-apps-provisioning-tutorial/gapps-auth.png)

7. No portal do Azure, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao G Suite. Se a conexão falhar, verifique se sua conta do G Suite tem permissões de administrador e tente novamente. Em seguida, tente a etapa **Autorizar** novamente.

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Provisionar usuários do Azure Active Directory**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o G Suite na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no G Suite para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API do G Suite dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |primaryEmail|String|
   |relation. [tipo EQ "Gerenciador"]. valor|String|
   |name.familyName|String|
   |name.givenName|String|
   |suspenso|String|
   |externalIds. [tipo EQ "personalizado"]. valor|String|
   |externalIds. [tipo EQ "organização"]. valor|String|
   |atende. [tipo EQ "trabalho"]. país|String|
   |atende. [tipo EQ "trabalho"]. streetAddress|String|
   |atende. [tipo EQ "trabalho"]. região|String|
   |atende. [tipo EQ "trabalho"]. localidade|String|
   |atende. [tipo EQ "trabalho"]. CEP|String|
   |e-mail. [tipo EQ "trabalho"]. endereço|String|
   |organizações. [tipo EQ "trabalho"]. departamento|String|
   |organizações. [tipo EQ "trabalho"]. título|String|
   |phoneNumbers. [tipo EQ "trabalho"]. valor|String|
   |phoneNumbers. [tipo EQ "móvel"]. valor|String|
   |phoneNumbers. [tipo EQ "work_fax"]. valor|String|
   |e-mail. [tipo EQ "trabalho"]. endereço|String|
   |organizações. [tipo EQ "trabalho"]. departamento|String|
   |organizações. [tipo EQ "trabalho"]. título|String|
   |phoneNumbers. [tipo EQ "trabalho"]. valor|String|
   |phoneNumbers. [tipo EQ "móvel"]. valor|String|
   |phoneNumbers. [tipo EQ "work_fax"]. valor|String|
   |atende. [tipo EQ "Home"]. país|String|
   |atende. [type EQ "Home"]. formatado|String|
   |atende. [type EQ "Home"]. localidade|String|
   |atende. [type EQ "Home"]. postalCode|String|
   |atende. [tipo EQ "Home"]. região|String|
   |atende. [type EQ "Home"]. streetAddress|String|
   |atende. [tipo EQ "other"]. país|String|
   |atende. [tipo EQ "other"]. formatado|String|
   |atende. [tipo EQ "other"]. localidade|String|
   |atende. [tipo EQ "other"]. postalCode|String|
   |atende. [tipo EQ "other"]. região|String|
   |atende. [tipo EQ "other"]. streetAddress|String|
   |atende. [tipo EQ "trabalho"]. formatado|String|
   |changePasswordAtNextLogin|String|
   |e-mail. [tipo EQ "Home"]. endereço|String|
   |e-mail. [tipo EQ "other"]. endereço|String|
   |externalIds. [tipo EQ "conta"]. valor|String|
   |externalIds. [tipo EQ "personalizado"]. CustomType|String|
   |externalIds. [tipo EQ "cliente"]. valor|String|
   |externalIds. [tipo EQ "login_id"]. valor|String|
   |externalIds. [tipo EQ "rede"]. valor|String|
   |gênero. Type|String|
   |GeneratedImmutableId|String|
   |Identificador|String|
   |IMS. [type EQ "Home"]. protocolo|String|
   |IMS. [tipo EQ "other"]. protocolo|String|
   |IMS. [tipo EQ "trabalho"]. protocolo|String|
   |includeInGlobalAddressList|String|
   |ipWhitelisted|String|
   |organizações. [tipo EQ "School"]. costCenter|String|
   |organizações. [tipo EQ "escola"]. departamento|String|
   |organizações. [tipo EQ "School"]. domínio|String|
   |organizações. [tipo EQ "School"]. fullTimeEquivalent|String|
   |organizações. [tipo EQ "escola"]. local|String|
   |organizações. [tipo EQ "escola"]. nome|String|
   |organizações. [tipo EQ "escola"]. símbolo|String|
   |organizações. [tipo EQ "escola"]. título|String|
   |organizações. [tipo EQ "trabalho"]. costCenter|String|
   |organizações. [tipo EQ "trabalho"]. domínio|String|
   |organizações. [tipo EQ "trabalho"]. fullTimeEquivalent|String|
   |organizações. [tipo EQ "trabalho"]. local|String|
   |organizações. [tipo EQ "trabalho"]. nome|String|
   |organizações. [tipo EQ "trabalho"]. símbolo|String|
   |OrgUnitPath|String|
   |phoneNumbers. [tipo EQ "Home"]. valor|String|
   |phoneNumbers. [tipo EQ "other"]. valor|String|
   |aos. [tipo EQ "Home"]. valor|String|
   |aos. [tipo EQ "other"]. valor|String|
   |aos. [tipo EQ "trabalho"]. valor|String|
   

10. Na seção **mapeamentos** , selecione **provisionar grupos de Azure Active Directory**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para o G Suite na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no G Suite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |email|String|
      |Membros|String|
      |name|String|
      |description|String|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o G Suite, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o G Suite escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

> [!NOTE]
> Se os usuários já tiverem uma conta pessoal/consumidor existente usando o endereço de email do usuário do Azure AD, isso poderá causar algum problema que poderia ser resolvido usando a ferramenta de transferência do Google antes de executar a sincronização de diretórios.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
