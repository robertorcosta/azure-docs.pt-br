---
title: 'Tutorial: Configurar Samanage para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Samanage.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 21a3c81d9a24cc63d3fc77c95c94df1e9113d292
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543441"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutorial: Configurar Samanage para provisionamento automático de usuário
Este tutorial descreve as etapas que você precisa executar tanto no Samanage quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [Samanage](https://www.samanage.com/pricing/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrate-to-the-new-samange-application"></a>Migrar para o novo aplicativo Samange

Se você tiver uma integração existente com o Samanage, consulte a seção abaixo sobre as alterações futuras. Se você estiver configurando o Samanage pela primeira vez, poderá ignorar esta seção e mover para os **recursos com suporte**.

#### <a name="whats-changing"></a>O que está sendo alterado?
* Alterações no lado do Azure AD: o método de autorização para provisionar usuários no Samange tem sido historicamente uma **autenticação básica**. Em breve, você verá o método de autorização alterado para **token secreto de vida longa**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>O que preciso fazer para migrar minha integração personalizada existente para o novo aplicativo?
Se você tiver uma integração Samanage existente com credenciais de administrador válidas, **nenhuma ação será necessária**. Migramos automaticamente os clientes para o novo aplicativo. Esse processo é feito por completo nos bastidores. Se as credenciais existentes expirarem ou se você precisar autorizar o acesso ao aplicativo novamente, será necessário gerar um token secreto de vida útil longa. Para gerar um novo token, consulte a etapa 2 deste artigo.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Como saber se meu aplicativo foi migrado? 
Quando seu aplicativo for migrado, na seção **credenciais de administrador** , os **campos nome de usuário** de administrador e senha de **administrador** serão substituídos por um único campo de **token secreto** .

## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Samanage
> * Remover usuários no Samanage quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Samanage
> * Provisionar grupos e associações de grupo no Samanage
> * [Logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) no Samanage (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global). 
* Um [locatário do Samanage](https://www.samanage.com/pricing/) com o pacote profissional.
* Uma conta de usuário no Samanage com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapeados entre o Azure AD e o Samanage](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-samanage-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Samanage para dar suporte ao provisionamento com o Azure AD

Para gerar um token secreto para autenticação, faça referência [a isso](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-samanage-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Samanage da Galeria de aplicativos do Azure AD

Adicione o Samanage da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no Samanage. Se você tiver configurado anteriormente o Samanage para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao Samanage, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-samanage"></a>Etapa 5. Configurar o provisionamento automático de usuário para o Samanage 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Samanage no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Samanage**.

    ![O link do Samanage na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://api.samanage.com` a **URL de locatário**.  Insira o valor do token secreto recuperado anteriormente em **Token Secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Samanage. Se a conexão falhar, verifique se sua conta do Samanage tem permissões de administrador e tente novamente

    ![provisionamento](./media/samanage-provisioning-tutorial/provisioning.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Samanage**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Samanage na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Samanage em operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API Samanage dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

      ![Mapeamentos de usuário Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Samanage**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para o Samanage na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder os grupos no Samanage em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      ![Mapeamentos de grupo Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Samanage, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos a serem provisionados para o Samanage escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Limitações do conector

Se você selecionar a opção **sincronizar todos os usuários e grupos** e configurar um valor para o atributo **funções** Samanage, o valor na caixa **valor padrão se nulo (é opcional)** deverá ser expresso no seguinte formato:

- {"displayName": "role"}, em que role é o valor padrão desejado.

## <a name="change-log"></a>Log de alterações

* 04/22/2020-método de autorização atualizado da autenticação básica para o token secreto de vida longa.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
