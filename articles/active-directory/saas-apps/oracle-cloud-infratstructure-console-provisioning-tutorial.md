---
title: 'Tutorial: Configure o Oracle Cloud Infrastructure Console para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Aprenda a provisionar e desprovisionar automaticamente contas de usuários do Azure AD para o Oracle Cloud Infrastructure Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378945"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Tutorial: Configure o Oracle Cloud Infrastructure Console para provisionamento automático do usuário

Este tutorial descreve as etapas que você precisa executar tanto no Oracle Cloud Infrastructure Console quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático do usuário. Quando configurado, o Azure AD fornece automaticamente e desprovisiona usuários e grupos para o [Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) usando o serviço de provisionamento AD do Azure. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Recursos suportados
> [!div class="checklist"]
> * Criar usuários no Oracle Cloud Infrastructure Console
> * Remova os usuários no Oracle Cloud Infrastructure Console quando eles não precisarem mais de acesso
> * Mantenha os atributos do usuário sincronizados entre o Azure AD e o Oracle Cloud Infrastructure Console
> * Grupos de provisão e membros de grupos no Oracle Cloud Infrastructure Console
> * [Único login no](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) Oracle Cloud Infrastructure Console (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos na nuvem, proprietário de aplicativos ou administrador global). 
* Um [inquilino](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)do Oracle Cloud Infrastructure Control .
* Uma conta de usuário no Controle de Infra-estrutura da Nuvem Oracle com permissões de administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje sua implantação de provisionamento
1. Saiba [como funciona o serviço de provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determine quem estará no [escopo para provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais dados [mapear entre o Azure AD e o Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configure o Oracle Cloud Infrastructure Console para suportar o provisionamento com o Azure AD

1. Faça login no portal de admin oracle Cloud Infrastructure Console. No canto superior esquerdo da tela navegue até a **Federação de > de Identidade**.

    ![Oracle Admin](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Clique na URL exibida na página ao lado do Oracle Identity Cloud Service Console.

    ![Oracle URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Clique em **Adicionar provedor de identidade** para criar um novo provedor de identidade. Salve o id Id Id para ser usado como parte da URL do inquilino. Clique no ícone mais ao lado da guia **Aplicativos** para criar um OAuth Client e Grant IDCS Identity Domain Administrator AppRole.

    ![Ícone da Nuvem Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Siga as capturas de tela abaixo para configurar seu aplicativo. Uma vez que a configuração seja concluída clique em **Salvar**.

    ![Configuração oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Política de emissão de tokens Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Na guia configurações do seu aplicativo, expanda a opção **Informações Gerais** para recuperar o ID do cliente e o sigilo do cliente.

    ![Geração de tokens Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Para gerar um token secreto Base64 codificar o ID do cliente e o cliente secreto no formato **ID do cliente:Client Secret**. Guarde o símbolo secreto. Esse valor será inserido no campo **Token Secreto** na guia de provisionamento do aplicativo Oracle Cloud Infrastructure Console no portal Azure.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Oracle Cloud Infrastructure Console da galeria de aplicativos Azure AD

Adicione o Oracle Cloud Infrastructure Console da galeria de aplicativos Azure AD para começar a gerenciar o provisionamento para o Oracle Cloud Infrastructure Console. Se você tiver configurado anteriormente o Oracle Cloud Infrastructure Console para SSO, você poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo na galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo para provisionamento 

O serviço de provisionamento Azure AD permite que você escopo que será provisionado com base na atribuição para o aplicativo e ou com base em atributos do usuário /grupo. Se você optar por escopo de quem será provisionado para o seu aplicativo com base na atribuição, você pode usar as [seguintes etapas](../manage-apps/assign-user-or-group-access-portal.md) para atribuir usuários e grupos ao aplicativo. Se você optar por escopo que será provisionado com base apenas em atributos do usuário ou grupo, você pode usar um filtro de escopo como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao Oracle Cloud Infrastructure Console, você deve selecionar uma função diferente **do Default Access**. Os usuários com a função Default Access são excluídos do provisionamento e serão marcados como não efetivamente habilitados nos registros de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você pode [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de ser lançado para todos. Quando o escopo para provisionamento é definido para usuários e grupos atribuídos, você pode controlá-lo atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Etapa 5. Configure o provisionamento automático do usuário para o Oracle Cloud Infrastructure Console 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o Oracle Cloud Infrastructure Console no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Console da Infraestrutura de Nuvem da Oracle**.

    ![O link oracle cloud infrastructure console na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de admin,** `https://<IdP ID>.identity.oraclecloud.com/admin/v1` insira a URL do **inquilino** no formato . Por exemplo, `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Insira o valor do token secreto recuperado anteriormente no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Oracle Cloud Infrastructure Console. Se a conexão falhar, certifique-se de que sua conta oracle Cloud Infrastructure Console tenha permissões de administração e tente novamente.

    ![provisionamento](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **Notificação enviar um e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do Azure Active Directory para o Oracle Cloud Infrastructure Console**.

9. Revise os atributos do usuário sincronizados do Azure AD para o Oracle Cloud Infrastructure Console na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Oracle Cloud Infrastructure Console para operações de atualização. Se você optar por alterar o [atributo de destino correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)você precisará garantir que a API do Console oracle cloud infrastructure suporte aos usuários filtrantes com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

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
      |endereços[tipo eq "work"].formatado|String|
      |endereços[tipo eq "trabalho"].localidade|String|
      |endereços[tipo eq "trabalho"].região|String|
      |addresses[type eq "work"].postalCode|String|
      |endereços[tipo eq "trabalho"].país|String|
      |addresses[type eq "work"].streetAddress|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNúmero|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:user:bypassNotificação|Boolean|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:isFederatedUser|Boolean|

10. Na seção **Mapeamentos,** selecione **Sincronizar Grupos de diretórios ativos do Azure para o Oracle Cloud Infrastructure Console**.

11. Revise os atributos de grupo sincronizados do Azure AD para o Oracle Cloud Infrastructure Console na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no Oracle Cloud Infrastructure Console para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para o Oracle Cloud Infrastructure Console, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o Oracle Cloud Infrastructure Console escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os usuários e grupos definidos no **Escopo** na seção **Configurações.** O ciclo inicial leva mais tempo para ser executado do que os ciclos subseqüentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD esteja sendo executado. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

* Use os [registros de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com sucesso ou sem sucesso
* Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e quão perto está da conclusão
* Se a configuração de provisionamento parecer estar em um estado insalubre, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
