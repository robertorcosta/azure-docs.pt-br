---
title: 'Tutorial: configurar o Apple School Manager para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Apple School Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: 221039ad1dc30e645d98ba6dd7e79c7b81ba3430
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313052"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Tutorial: configurar o Apple School Manager para o provisionamento automático de usuário



Este tutorial descreve as etapas que você precisa executar tanto no Apple School Manager quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente os usuários para o [Apple School Manager](https://school.apple.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Apple School Manager
> * Remover usuários no Apple School Manager quando eles não precisam mais de acesso
> * Manter atributos de usuário específicos sincronizados entre o Azure AD e o Apple School Manager

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global). 
* Uma conta do Apple School Manager com a função de administrador, gerente de site ou gerente de pessoas.

> [!NOTE]
> A transferência de token para o Azure AD e o estabelecimento de uma conexão bem-sucedida deve ser concluída em quatro dias do calendário ou o processo precisa ser iniciado novamente.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapear entre o Azure AD e o Apple School Manager](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Apple School Manager para dar suporte ao provisionamento com o Azure AD

1. No Apple School Manager, entre com uma conta que tenha a função de administrador, Gerenciador de sites ou gerente de pessoas.
2. Clique em configurações na parte inferior da barra lateral clique em fonte de dados abaixo de configurações da organização e, em seguida, clique em conectar à fonte de dados.
3. Clique em conectar ao lado de SCIM, leia atentamente o aviso, clique em copiar e em fechar.
[A janela conectar ao SCIM, que fornece um token e um botão de cópia abaixo dele.] Deixe essa janela aberta para copiar a URL do locatário do Apple Business Manager para o Azure AD, que é: https://federation.apple.com/feeds/school/scim

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> O token secreto não deve ser compartilhado com ninguém que não seja o administrador do Azure AD.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Apple School Manager da Galeria de aplicativos do Azure AD

Adicione o Apple School Manager da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para o Apple School Manager. Se você tiver configurado anteriormente o Apple School Manager para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários ao Apple School Manager, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Etapa 5. Configurar o provisionamento automático de usuário para o Apple School Manager

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Apple School Manager**.

    ![O Apple School Manager na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia de provisionamento automático](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a **URL base do scim 2,0 e** os valores de token de acesso recuperados do Apple School Manager na **URL do locatário** e o **token secreto** , respectivamente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Apple School Manager. Se a conexão falhar, verifique se sua conta do Apple School Manager tem permissões de administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Se a conexão for bem-sucedida, o Apple School Manager mostrará a conexão SCIM como ativa. Esse processo pode levar até 60 segundos para que o Apple School Manager reflita o status de conexão mais recente.

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários com o Apple School Manager**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Apple School Manager na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Apple School Manager para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |ativo|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.givenName|String|
   |externalId|String|
   |localidade|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Apple School Manager, altere o **status de provisionamento** para **ativado** na seção Configurações.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o Apple School Manager escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação

Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Examinar os requisitos do SCIM para o Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Como uma ID de pessoa é usada no Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [Usar o SCIM para importar usuários para o Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Resolver conflitos de conta de usuário SCIM no Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Excluir contas do Azure AD que aparecem no Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [Exibir a atividade do SCIM no Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Gerenciar o token SCIM e as conexões existentes no Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [Desconectar a conexão SCIM no Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [Gerenciar o token SCIM e as conexões existentes no Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apdc9a8236e9)
* [Solucionando problemas de conexão do SCIM no Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
