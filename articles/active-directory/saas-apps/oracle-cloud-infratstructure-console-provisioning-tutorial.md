---
title: 'Tutorial: configurar o console de infraestrutura de nuvem da Oracle para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o console de infraestrutura de nuvem da Oracle.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 665e4870619751bbda062473d0c2549b26352d94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361595"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Tutorial: configurar o console de infraestrutura de nuvem da Oracle para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no console de infraestrutura de nuvem da Oracle e no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [console de infraestrutura de nuvem da Oracle](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no console de infraestrutura de nuvem da Oracle
> * Remova os usuários no console de infraestrutura de nuvem da Oracle quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o console de infraestrutura de nuvem da Oracle
> * Provisionar grupos e associações de grupo no console de infraestrutura de nuvem da Oracle
> * [Logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) no console de infraestrutura de nuvem da Oracle (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global). 
* Um [locatário](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)de controle de infraestrutura de nuvem da Oracle.
* Uma conta de usuário no controle de infraestrutura de nuvem da Oracle com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapear entre o Azure AD e o console de infraestrutura de nuvem da Oracle](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o console de infraestrutura de nuvem da Oracle para dar suporte ao provisionamento com o Azure AD

1. Faça logon no portal de administração do console de infraestrutura do Oracle Cloud. No canto superior esquerdo da tela, navegue até **identidade > Federação**.

    ![Administrador Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Clique na URL exibida na página ao lado do console do serviço de nuvem de identidade do Oracle.

    ![URL do Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Clique em **Adicionar provedor de identidade** para criar um novo provedor de identidade. Salve a ID do IdP a ser usada como parte da URL do locatário. Clique no ícone de adição ao lado da guia **aplicativos** para criar um cliente OAuth e conceder ao administrador de domínio de identidade IDCS AppRole.

    ![Ícone de nuvem Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Siga as capturas de tela abaixo para configurar seu aplicativo. Quando a configuração for concluída, clique em **salvar**.

    ![Configuração do Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Política de emissão de token do Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Na guia Configurações do seu aplicativo, expanda a opção **informações gerais** para recuperar a ID do cliente e o segredo do cliente.

    ![Geração de token do Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Para gerar um token secreto base64, codifique a ID do cliente e o segredo do cliente no formato **ID do cliente: segredo do cliente**. Salve o token secreto. Esse valor será inserido no campo **token secreto** na guia provisionamento do aplicativo de console de infraestrutura de nuvem da Oracle na portal do Azure.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o console de infraestrutura de nuvem da Oracle da Galeria de aplicativos do Azure AD

Adicione o console de infraestrutura de nuvem da Oracle da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para o console de infraestrutura de nuvem da Oracle. Se você tiver configurado anteriormente o console de infraestrutura de nuvem do Oracle para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao console de infraestrutura de nuvem da Oracle, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Etapa 5. Configurar o provisionamento automático de usuário para o console de infraestrutura de nuvem da Oracle 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o console de infraestrutura de nuvem da Oracle no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Console da Infraestrutura de Nuvem da Oracle**.

    ![O link do console de infraestrutura de nuvem da Oracle na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário** no formato `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Por exemplo, `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Insira o valor do token secreto recuperado anteriormente em **Token Secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao console de infraestrutura de nuvem da Oracle. Se a conexão falhar, verifique se sua conta do console de infraestrutura de nuvem do Oracle tem permissões de administrador e tente novamente.

    ![Captura de tela mostra a caixa de diálogo credenciais de administrador, em que é possível inserir seu locatário U R L e token secreto.](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o console de infraestrutura de nuvem da Oracle**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o console de infraestrutura de nuvem da Oracle na seção **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no console de infraestrutura de nuvem da Oracle para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API do console de infraestrutura de nuvem da Oracle ofereça suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |userName|String|
      |ativo|Boolean|
      |título|String|
      |emails[type eq "work"].value|String|
      |preferredLanguage|String|
      |name.givenName|String|
      |name.familyName|String|
      |addresses[type eq "work"].formatted|String|
      |addresses[type eq "work"].locality|String|
      |addresses[type eq "work"].region|String|
      |addresses[type eq "work"].postalCode|String|
      |addresses[type eq "work"].country|String|
      |addresses[type eq "work"].streetAddress|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
      |urn: IETF: params: SCIM: schemas: Oracle: IDCs: Extension: User: User: bypassNotification|Boolean|
      |urn: IETF: params: SCIM: schemas: Oracle: IDCs: Extension: User: User: isFederatedUser|Boolean|

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o console de infraestrutura de nuvem da Oracle**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para o console de infraestrutura de nuvem da Oracle na seção **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no console de infraestrutura de nuvem da Oracle para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o console de infraestrutura de nuvem do Oracle, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o console de infraestrutura de nuvem da Oracle escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

* Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não
* Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
