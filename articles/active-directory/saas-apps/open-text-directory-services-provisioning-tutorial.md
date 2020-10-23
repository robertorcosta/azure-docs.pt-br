---
title: 'Tutorial: configurar os serviços de diretório do OpenText para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para os serviços de diretório do OpenText.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: c0858c2e5b1ada866d252e45113f0f90b73caaf8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428807"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Tutorial: configurar os serviços de diretório do OpenText para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto nos serviços de diretório da OpenText quanto no Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para os serviços de diretório do OpenText usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários nos serviços de diretório da OpenText
> * Remova os usuários nos serviços de diretório do OpenText quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre os serviços de diretório do Azure AD e OpenText
> * Provisionar grupos e associações de grupo nos serviços de diretório da OpenText
> * [Logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial) para serviços de diretório OpenText (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Uma instalação do OTDS acessível pelo Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapear entre os serviços de diretório do Azure AD e OpenText](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar os serviços de diretório da OpenText para dar suporte ao provisionamento com o Azure AD

> [!NOTE]
> As etapas a seguir se aplicam a uma instalação dos serviços de diretório do OpenText. Eles não se aplicam a locatários CoreShare ou OpenText OT2 de OpenText.

1. Crie um **cliente OAuth**confidencial dedicado.
2. Definir um tempo de **vida de token de acesso**longo.

      ![Vida útil do token de acesso](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. Não especifique nenhuma URL de redirecionamento. Eles não são necessários. 
4. OTDS irá gerar e exibir o **segredo do cliente**. Salve a **ID do cliente** e o **segredo do cliente** em um local seguro.

      ![Segredo do cliente](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Crie uma partição para os usuários e grupos a serem sincronizados do Azure AD.

      ![Página de partição](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Conceda direitos administrativos ao cliente OAuth que você criou na partição que será usada para os usuários e grupos do Azure AD que estão sendo sincronizados.    
      * Ações de > de partição-> editar administradores

      ![Página do administrator](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. Um token secreto deve ser recuperado e configurado no Azure AD. Qualquer aplicativo cliente HTTP pode ser usado para isso. Abaixo estão as etapas para recuperar usando o aplicativo de API do Swagger incluído no OTDS.
      * Em um navegador da Web, acesse {OTDS URL}/otdsws/oauth2
      * Vá para/token e clique no ícone de cadeado na parte superior direita. Insira a ID do cliente OAuth e o segredo recuperados anteriormente como o nome de usuário e a senha, respectivamente. Clique em Autorizar.

      ![Botão de autorização](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Selecione **client_credentials** para o grant_type e clique em **executar**.

      ![Executar botão](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. O token de acesso na resposta deve ser usado no campo **token secreto** no Azure AD.

      ![Token de acesso](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar serviços de diretório OpenText da Galeria de aplicativos do Azure AD

Adicione serviços de diretório OpenText da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para os serviços de diretório do OpenText. Se você tiver configurado anteriormente os serviços de diretório do OpenText para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos aos serviços de diretório OpenText, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Etapa 5. Configurar o provisionamento automático de usuário para os serviços de diretório OpenText 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para serviços de diretório OpenText no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Serviços de Diretório OpenText**.

    ![O link de serviços de diretório OpenText na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia de provisionamento automático](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do locatário dos serviços de diretório do OpenText
   * URL de locatário não específico: {URL OTDS}/scim/{partitionName}
   * URL de locatário específica: {OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. Insira o token secreto recuperado da etapa 2. Clique em **testar conexão** para garantir que o Azure ad possa se conectar aos serviços de diretório do OpenText. Se a conexão falhar, verifique se sua conta de serviços de diretório do OpenText tem permissões de administrador e tente novamente.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários aos serviços de diretório do OpenText**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para os serviços de diretório OpenText na seção **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário nos serviços de diretório da OpenText para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API de serviços de diretório do OpenText dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |ativo|Boolean|
   |displayName|String|
   |título|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência| 

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para serviços de diretório OpenText**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para os serviços de diretório OpenText na seção **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos nos serviços de diretório da OpenText para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para os serviços de diretório do OpenText, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para os serviços de diretório do OpenText escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

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
