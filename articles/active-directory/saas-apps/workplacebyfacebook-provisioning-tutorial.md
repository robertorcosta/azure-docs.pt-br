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
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603214"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Tutorial: configurar o Workplace by Facebook para provisionamento automático de usuários

Este tutorial descreve as etapas que você precisa executar tanto no Workplace by Facebook quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático do usuário. Quando configurado, o Azure AD fornece automaticamente e desprovisiona usuários e grupos para [o Workplace pelo Facebook](https://work.workplace.com/) usando o serviço de provisionamento Ad do Azure. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migrando para o novo local de trabalho pelo aplicativo do Facebook
Se você tiver uma integração existente com o Workplace pelo Facebook, consulte a seção abaixo sobre as mudanças que estão por vir. Se você estiver configurando o Workplace by Facebook pela primeira vez, você pode pular essa seção e passar para os recursos suportados. 

#### <a name="whats-changing"></a>O que está sendo alterado?
* Alterações no lado Azure AD: O método de autorização para provisionar usuários no Local de Trabalho tem sido historicamente um token secreto de longa duração. Em breve você verá o método de autorização alterado para a concessão de autorização da OAuth. 
* Mudanças no lado do Local de trabalho: Anteriormente, o aplicativo Azure AD era uma integração personalizada no Workplace pelo Facebook. Agora você verá o Azure AD no diretório de integrações do Local de trabalho como um aplicativo de terceiros. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>O que preciso fazer para migrar minha integração personalizada existente para o novo aplicativo?
Se você tiver uma integração no Local de Trabalho existente com um token válido, **nenhuma ação será necessária**. Estamos migrando automaticamente clientes a cada semana para o novo aplicativo. Isso é feito completamente nos bastidores. Se você não puder esperar e quiser mudar para o novo aplicativo manualmente, você pode adicionar uma nova instância do Workplace da galeria e configurar o provisionamento novamente. Todas as novas instâncias do Workplace usarão automaticamente a nova versão do aplicativo. 

 
Se a integração no local de trabalho estiver em quarentena, você precisará fornecer um token válido novamente para que migremos você. A seção de credenciais de admin será acinzentada, mas você pode anexar o seguinte (**? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=verdadeiro**) para sua URL para salvar credenciais novamente. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Como posso dizer se minha inscrição foi migrada? 
Quando seu aplicativo for migrado, o banner na seção de autorização sobre alterações de upcomming será removido e o campo de token secreto será substituído por um botão de autorização azul. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>A seção de credenciais de admin está acinzentada na minha aplicação e eu não posso salvar. Por quê?
Bloqueamos a seção de credenciais de admin para clientes existentes no Local de Trabalho. Quando o seu inquilino tiver migrado para o novo aplicativo Workplace, você poderá atualizar novamente a seção de credenciais de admin. Se você não puder esperar, você pode usar a URL acima para editar seu aplicativo. 

 
#### <a name="when-will-these-changes-happen"></a>Quando essas mudanças acontecerão?
Todas as novas instâncias do Workplace já estarão utilizando o novo método de integração/autorização. As integrações existentes serão migradas gradualmente até maio. A equipe do local de trabalho forneceu uma prorrogação do prazo de 28 de fevereiro a 1º de maio. 

## <a name="capabilities-supported"></a>Recursos suportados
> [!div class="checklist"]
> * Criar usuários no Local de Trabalho pelo Facebook
> * Remova os usuários no Local de Trabalho pelo Facebook quando eles não exigem mais acesso
> * Mantenha os atributos do usuário sincronizados entre a Azure AD e o Workplace pelo Facebook
> * [Único login no](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) Workplace pelo Facebook (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos na nuvem, proprietário de aplicativos ou administrador global)
* Uma assinatura do Workplace by Facebook habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje sua implantação de provisionamento
1. Saiba [como funciona o serviço de provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determine quem estará no [escopo para provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais dados [mapear entre o Azure AD e o Workplace pelo Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configure workplace by Facebook para suportar provisionamento com a Azure AD

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo Workplace by Facebook. Depois de decidir, atribua esses usuários ao seu aplicativo Workplace by Facebook seguindo estas instruções:

*   Recomendamos a atribuição de um único usuário do Azure AD ao Workplace by Facebook para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Workplace by Facebook, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar workplace by Facebook a partir da galeria de aplicativos Azure AD

Adicione workplace by Facebook a partir da galeria de aplicativos Azure AD para começar a gerenciar o provisionamento para o Local de Trabalho pelo Facebook. Se você já configurou anteriormente workplace by Facebook para SSO, você pode usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo na galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo para provisionamento 

O serviço de provisionamento Azure AD permite que você escopo que será provisionado com base na atribuição para o aplicativo e ou com base em atributos do usuário /grupo. Se você optar por escopo de quem será provisionado para o seu aplicativo com base na atribuição, você pode usar as [seguintes etapas](../manage-apps/assign-user-or-group-access-portal.md) para atribuir usuários e grupos ao aplicativo. Se você optar por escopo que será provisionado com base apenas em atributos do usuário ou grupo, você pode usar um filtro de escopo como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao Workplace pelo Facebook, você deve selecionar uma função diferente **do Default Access**. Os usuários com a função Default Access são excluídos do provisionamento e serão marcados como não efetivamente habilitados nos registros de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você pode [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de ser lançado para todos. Quando o escopo para provisionamento é definido para usuários e grupos atribuídos, você pode controlá-lo atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workplace by Facebook**.

    ![O link Workplace by Facebook na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de **Admin,** clique em **Autorizar**. Você será redirecionado para o Local de Trabalho pela página de autorização do Facebook. Insira seu local de trabalho pelo nome de usuário do Facebook e clique no botão **Continuar.** Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Workplace pelo Facebook. Se a conexão falhar, certifique-se de que sua conta do Facebook tenha permissões de administração e tente novamente.

    ![provisionamento](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![Autorizar](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. No campo **E-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **Notificação enviar um e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do Diretório Ativo do Azure no Local de Trabalho pelo Facebook**.

9. Revise os atributos do usuário sincronizados do Azure AD para o Workplace pelo Facebook na seção **Mapeamento de atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Workplace by Facebook em operações de atualização. Se você optar por alterar o [atributo de destino correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)você precisará garantir que a API do Facebook suporte a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |ativo|Boolean|
   |título|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |endereços[tipo eq "work"].formatado|String|
   |addresses[type eq "work"].streetAddress|String|
   |endereços[tipo eq "trabalho"].localidade|String|
   |endereços[tipo eq "trabalho"].região|String|
   |endereços[tipo eq "trabalho"].país|String|
   |addresses[type eq "work"].postalCode|String|
   |endereços[tipo eq "outro"].formatado|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento Azure AD para O Local de Trabalho pelo Facebook, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de prosentar no Workplace pelo Facebook, escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os usuários e grupos definidos no **Escopo** na seção **Configurações.** O ciclo inicial leva mais tempo para ser executado do que os ciclos subseqüentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD esteja sendo executado. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

1. Use os [registros de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e quão perto está da conclusão
3. Se a configuração de provisionamento parecer estar em um estado insalubre, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
*  Se você vir um usuário criado sem sucesso e houver um evento de registro de auditoria com o código "1789003", significa que o usuário é de um domínio não verificado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
