---
title: 'Tutorial: configurar o Workplace by Facebook para o provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99103c9994b240e2f45b66acf269b320c90e5135
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231723"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Tutorial: configurar o Workplace by Facebook para provisionamento automático de usuários

Este tutorial descreve as etapas que você precisa executar no local de trabalho por Facebook e Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos no [local de trabalho pelo Facebook](https://work.workplace.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migrando para o novo aplicativo Workplace by Facebook
Se você tiver uma integração existente com o Workplace by Facebook, consulte a seção abaixo sobre as alterações recebidas. Se você estiver configurando o Workplace by Facebook pela primeira vez, poderá ignorar esta seção e mover para os recursos com suporte. 

#### <a name="whats-changing"></a>O que está sendo alterado?
* Alterações no lado do Azure AD: o método de autorização para provisionar usuários no local de trabalho tem sido historicamente um token secreto de vida longa. Em breve, você verá o método de autorização alterado para a concessão de autorização OAuth. 
* Alterações no lado do local de trabalho: anteriormente, o aplicativo Azure AD era uma integração personalizada no workplace by Facebook. Agora você verá o Azure AD no diretório de integrações do local de trabalho como um aplicativo de terceiros. 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>O que preciso fazer para migrar minha integração personalizada existente para o novo aplicativo?
Se você tiver uma integração de local de trabalho existente com um token válido, nenhuma ação será necessária. **A partir de 04/28/2020, migramos automaticamente todos os aplicativos que não estão em quarentena devido a credenciais inválidas.**
 
#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Como saber se meu aplicativo foi migrado? 
* No portal do Azure: quando seu aplicativo for migrado, a faixa na seção autorização sobre alterações futuras será removida e o campo token secreto será substituído por um botão autorizar azul. 
* No portal local de trabalho pelo Facebook: examine o aplicativo Azure AD para garantir que ele seja aprovado.  

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>A seção de credenciais de administrador está esmaecida no meu aplicativo e não consigo salvar. Por quê?
Bloqueamos a seção de credenciais de administrador para clientes do local de trabalho que não foram migrados. Use a URL a seguir se a seção de credenciais de administrador estiver esmaecida e você precisar autorizar o acesso novamente. **? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride = true** (https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true)


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no workplace by Facebook
> * Remover usuários do workplace by Facebook quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Workplace by Facebook
> * [Logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) no workplace by Facebook (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global)
* Uma assinatura do Workplace by Facebook habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planejar sua implantação de provisionamento
1. Saiba mais sobre [como o serviço de provisionamento funciona](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapeados entre o Azure AD e o Workplace by Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Workplace by Facebook para dar suporte ao provisionamento com o Azure AD

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo Workplace by Facebook. Depois de decidir, atribua esses usuários ao seu aplicativo Workplace by Facebook seguindo estas instruções:

*   Recomendamos a atribuição de um único usuário do Azure AD ao Workplace by Facebook para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Workplace by Facebook, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Workplace by Facebook da Galeria de aplicativos do Azure AD

Adicione o Workplace by Facebook da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no workplace by Facebook. Se você tiver configurado anteriormente o Workplace by Facebook para SSO, poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo da Galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se você optar por definir o escopo que será provisionado em seu aplicativo com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você escolher o escopo que será provisionado com base apenas em atributos do usuário ou grupo, você poderá usar um filtro de escopo conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao Workplace by Facebook, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido como todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workplace by Facebook**.

    ![O link Workplace by Facebook na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , clique em **autorizar**. Você será redirecionado para o Workplace pela página de autorização do Facebook. Insira o nome de usuário do seu local de trabalho por Facebook e clique no botão **continuar** . Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Workplace by Facebook. Se a conexão falhar, verifique se a conta do seu local de trabalho por Facebook tem permissões de administrador e tente novamente.

    ![provisionamento](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![Autorizar](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários no workplace pelo Facebook**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Workplace by Facebook na seção **mapeamento de atributo** . Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Workplace by Facebook em operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API do workplace by Facebook dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |ativo|Booliano|
   |title|Booliano|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome. formatado|String|
   |endereços [tipo EQ "trabalho"]. formatado|String|
   |addresses[type eq "work"].streetAddress|String|
   |endereços [tipo EQ "trabalho"]. localidade|String|
   |endereços [tipo EQ "trabalho"]. região|String|
   |endereços [tipo EQ "trabalho"]. país|String|
   |addresses[type eq "work"].postalCode|String|
   |endereços [tipo EQ "other"]. formatado|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Manager|String|
   |urn: IETF: params: SCIM: esquemas: extensão: Enterprise: 2.0: User: Department|String|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Workplace by Facebook, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar no workplace by Facebook escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia o ciclo de sincronização inicial de todos os usuários e grupos definidos no **escopo** na seção **configurações** . O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e como fechá-lo para conclusão
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os Estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
*  Se você vir um usuário não criado com êxito e houver um evento de log de auditoria com o código "1789003", isso significa que o usuário é de um domínio não verificado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
