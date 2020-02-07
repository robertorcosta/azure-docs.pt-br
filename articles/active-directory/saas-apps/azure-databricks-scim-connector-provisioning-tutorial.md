---
title: 'Tutorial: configurar Azure Databricks conector do SCIM para o provisionamento automático de usuário com Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para Azure Databricks conector do SCIM.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: de60b4ea1b09998e84bab4d204e3c8c3bc8779a4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050441"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Tutorial: configurar Azure Databricks conector do SCIM para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar em ambos Azure Databricks conector do SCIM e Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuários. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [Azure Databricks conector scim](https://databricks.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no conector do Azure Databricks SCIM
> * Remover usuários no conector Azure Databricks SCIM quando eles não precisam mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o conector SCIM do Azure Databricks
> * Provisionar grupos e associações de grupo no conector do SCIM Azure Databricks

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global). 
* Uma conta de Azure Databricks com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planejar sua implantação de provisionamento
1. Saiba mais sobre [como o serviço de provisionamento funciona](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapeados entre o Azure AD e o conector scim do Azure Databricks](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar Azure Databricks conector do SCIM para dar suporte ao provisionamento com o Azure AD

1. Para configurar Azure Databricks provisionamento do SCIM, adicione-o como um recurso em seu locatário do Azure Active Directory e configure-o usando as configurações abaixo.

    ![Azure Databricks configuração](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Para gerar um token de acesso pessoal no Azure Databricks consulte [isso](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management).

3. Copie o **token**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo de conector do Azure Databricks SCIM no portal do Azure.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar Azure Databricks conector do SCIM da Galeria de aplicativos do Azure AD

Adicione Azure Databricks conector SCIM da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para Azure Databricks conector do SCIM. Se você tiver configurado anteriormente Azure Databricks conector do SCIM para SSO, poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo da Galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Definir quem estará no escopo para provisionamento 

O serviço de provisionamento do Azure AD permite o escopo que será provisionado com base na atribuição ao aplicativo e ou com base em atributos do usuário/grupo. Se você optar por definir o escopo que será provisionado em seu aplicativo com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você escolher o escopo que será provisionado com base apenas em atributos do usuário ou grupo, você poderá usar um filtro de escopo conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos para Azure Databricks conector SCIM, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e serão marcados como não habilitados com eficiência nos logs de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de distribuir para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido como todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Etapa 5. Configurar o provisionamento automático de usuário para Azure Databricks conector SCIM 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

> [!NOTE]
> Para saber mais sobre Azure Databricks ponto de extremidade SCIM, confira [isso](https://docs.databricks.com/dev-tools/api/latest/scim.html
).

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Azure Databricks conector do SCIM no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Azure Databricks conector do scim**.

    ![O link do conector do Azure Databricks SCIM na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira seu Azure Databricks credenciais de administrador do conector scim e o nome de usuário. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao conector do scim Azure Databricks. Se a conexão falhar, verifique se a conta do conector do Azure Databricks SCIM tem permissões de administrador e tente novamente.

    ![provisionamento](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Azure Databricks conector scim**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para Azure Databricks conector do SCIM na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no conector de Azure Databricks scim para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que o Azure Databricks API do conector do scim dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |displayName|String|
   |ativo|Boolean|

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Azure Databricks conector scim**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para Azure Databricks conector SCIM na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no conector de Azure Databricks scim para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

     |Atributo|Tipo|
     |---|---|
     |displayName|String|
     |membros|Referência|

11. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Azure Databricks conector scim**.

12. Para habilitar o serviço de provisionamento do Azure AD para Azure Databricks conector SCIM, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você deseja provisionar para Azure Databricks conector SCIM escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia o ciclo de sincronização inicial de todos os usuários e grupos definidos no **escopo** na seção **configurações** . O ciclo inicial leva mais tempo para ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

* Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou sem êxito
* Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e como fechá-lo para conclusão
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os Estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
* O databricks sempre converte seus valores de nome de usuário em letras minúsculas ao salvar em seu diretório, independentemente da capitalização que enviamos a eles por meio de SCIM.
* Atualmente, as solicitações em relação ao Azure Databricks ' API SCIM para usuários diferenciam maiúsculas de minúsculas, portanto, se consultarmos por USER@contoso.com, ela será exibida com 0 resultados à medida que eles forem armazenados como user@contoso.com.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
