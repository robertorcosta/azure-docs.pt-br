---
title: 'Tutorial: configurar o SmartSheet para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o SmartSheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 623ec6999add175e85f117e547fba61734d2b892
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285974"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutorial: configurar o SmartSheet para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no SmartSheet e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no [SmartSheet](https://www.smartsheet.com/pricing). Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no SmartSheet
> * Remover usuários no SmartSheet quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o SmartSheet
> * Logon único no SmartSheet (recomendado)

> [!NOTE]
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global).
* [Um locatário do SmartSheet](https://www.smartsheet.com/pricing).
* Uma conta de usuário em um plano do SmartSheet Enterprise ou Enterprise Premier com permissões de administrador do sistema.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapeados entre o Azure AD e o SmartSheet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o SmartSheet para dar suporte ao provisionamento com o Azure AD

Antes de configurar o SmartSheet para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no SmartSheet.

1. Entre como um **sysadmin** no portal do **[SmartSheet](https://app.smartsheet.com/b/home)** e navegue até **administrador da conta**.

    ![Administrador da conta do SmartSheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Vá para **controles de segurança > provisionamento automático do usuário > editar**.

    ![Controles de segurança SmartSheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adicione e valide os domínios de email para os usuários que você planeja provisionar do Azure AD para o SmartSheet. Escolha **não habilitado** para garantir que todas as ações de provisionamento sejam originadas somente do Azure AD e também para garantir que sua lista de usuários do SmartSheet esteja em sincronia com as atribuições do Azure AD.

    ![Provisionamento de usuário do SmartSheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Depois que a validação for concluída, você precisará ativar o domínio. 

    ![SmartSheet ativar domínio](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Gere o **token secreto** necessário para configurar o provisionamento automático de usuário com o Azure ad navegando para **aplicativos e integrações**.

    ![Captura de tela da página de administração do SmartSheet com o avatar do usuário e a opção aplicativos & integrações chamada out.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Escolha **acesso à API**. Clique em **gerar novo token de acesso**.

    ![Captura de tela da caixa de diálogo configurações pessoais com o acesso à API e gerar novas opções de token de acesso chamadas.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Defina o nome do token de acesso à API. Clique em **OK**.

    ![Captura de tela da etapa 1 de 2: gerar token de acesso de API com a opção OK chamada out.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copie o token de acesso da API e salve-o, pois esse será o único momento que você pode exibi-lo. Isso é necessário no campo **token secreto** no Azure AD.

    ![Token SmartSheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o SmartSheet da Galeria de aplicativos do Azure AD

Adicione o SmartSheet da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no SmartSheet. Se você tiver configurado anteriormente o SmartSheet para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao SmartSheet, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Para garantir a paridade nas atribuições de função de usuário entre o SmartSheet e o Azure AD, é recomendável utilizar as mesmas atribuições de função preenchidas na lista completa de usuários do SmartSheet. Para recuperar essa lista de usuários do SmartSheet, navegue até **administrador da conta > gerenciamento de usuários > mais ações > baixar a lista de usuários (CSV)**.

* Para acessar determinados recursos no aplicativo, o SmartSheet exige que um usuário tenha várias funções. Para saber mais sobre tipos de usuário e permissões no SmartSheet, vá para [tipos de usuário e permissões](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se um usuário tiver várias funções atribuídas no SmartSheet, você **deverá** garantir que essas atribuições de função sejam replicadas no Azure ad para evitar um cenário em que os usuários possam perder o acesso a objetos SmartSheet permanentemente. Cada função exclusiva em SmartSheet **deve** ser atribuída a um grupo diferente no Azure AD. O usuário **deve** então ser adicionado a cada um dos grupos correspondentes às funções desejadas. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Etapa 5. Configurar o provisionamento automático de usuário para o SmartSheet 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no SmartSheet com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para SmartSheet no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SmartSheet**.

    ![O link do SmartSheet na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a **URL base do scim 2,0 e os valores de token de acesso** recuperados anteriormente de SmartSheet na URL do **locatário** e no **token secreto** , respectivamente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao SmartSheet. Se a conexão falhar, verifique se sua conta do SmartSheet tem permissões SysAdmin e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para SmartSheet**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o SmartSheet na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no SmartSheet para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |ativo|Boolean|
   |título|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |funções [EQ principal "true"]. display|Cadeia de caracteres|
   |funções [EQ principal "true"]. Type|String|
   |roles[primary eq "True"].value|String|
   |funções|String|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o SmartSheet, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o SmartSheet escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Limitações do conector

* SmartSheet não dá suporte a exclusões reversível. Quando o atributo **ativo** de um usuário é definido como false, o SmartSheet exclui o usuário permanentemente.

## <a name="change-log"></a>Log de alterações

* 06/16/2020-suporte adicionado para atributos de extensão Enterprise "centro de custo", "divisão", "gerente" e "departamento" para usuários.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
