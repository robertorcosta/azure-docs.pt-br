---
title: 'Tutorial: Configurar ServiceNow para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205090"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: Configure servicenow para provisionamento automático do usuário

Este tutorial descreve as etapas que você precisa executar tanto no ServiceNow quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático do usuário. Quando configurado, o Azure AD fornece automaticamente e desprovisiona usuários e grupos para [serviceNow](https://www.servicenow.com/) usando o serviço de provisionamento Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Recursos suportados
> [!div class="checklist"]
> * Criar usuários no ServiceNow
> * Remova os usuários no ServiceNow quando eles não precisarem mais de acesso
> * Mantenha os atributos do usuário sincronizados entre o Azure AD e o ServiceNow
> * Grupos de provisão e membros de grupos no ServiceNow
> * [Único login no](servicenow-tutorial.md) ServiceNow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos na nuvem, proprietário de aplicativos ou administrador global). 
* Um [exemplo servicenow](https://www.servicenow.com/) de Calgary ou superior
* Uma [instância servicenow express](https://www.servicenow.com/) de Helsinque ou superior
* Uma conta de usuário no ServiceNow com a função de administração

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje sua implantação de provisionamento
1. Saiba [como funciona o serviço de provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determine quem estará no [escopo para provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais dados [a mapear entre o Azure AD e o ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configure o ServiceNow para suportar o provisionamento com o Azure AD

1. Identifique o nome da instância do ServiceNow. Você pode encontrar o nome de ocorrência na URL que você usa para acessar o ServiceNow. No exemplo abaixo, o nome da ocorrência é dev35214.

![Exemplo servicenow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Obtenha credenciais para um admin no ServiceNow. Navegue até o perfil do usuário no ServiceNow e verifique se o usuário tem a função de admin. 

![Função de admin ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar serviceNow na galeria de aplicativos azure AD

Adicione o ServiceNow da galeria de aplicativos Azure AD para começar a gerenciar o provisionamento ao ServiceNow. Se você tiver configurado anteriormente o ServiceNow para SSO, você poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo na galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo para provisionamento 

O serviço de provisionamento Azure AD permite que você escopo que será provisionado com base na atribuição para o aplicativo e ou com base em atributos do usuário /grupo. Se você optar por escopo de quem será provisionado para o seu aplicativo com base na atribuição, você pode usar as [seguintes etapas](../manage-apps/assign-user-or-group-access-portal.md) para atribuir usuários e grupos ao aplicativo. Se você optar por escopo que será provisionado com base apenas em atributos do usuário ou grupo, você pode usar um filtro de escopo como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir usuários e grupos ao ServiceNow, você deve selecionar uma função diferente **do Default Access**. Os usuários com a função Default Access são excluídos do provisionamento e serão marcados como não efetivamente habilitados nos registros de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você pode [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de ser lançado para todos. Quando o escopo para provisionamento é definido para usuários e grupos atribuídos, você pode controlá-lo atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Etapa 5. Configure o provisionamento automático do usuário para o ServiceNow 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para serviceNow no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ServiceNow**.

    ![O link do ServiceNow na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de **Admin,** insira suas credenciais de admin serviceNow e nome de usuário. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao ServiceNow. Se a conexão falhar, certifique-se de que sua conta serviceNow tenha permissões de administração e tente novamente.

    ![provisionamento](./media/servicenow-provisioning-tutorial/provisioning.png)

6. No campo **E-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **Notificação enviar um e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para ServiceNow**.

9. Revise os atributos do usuário sincronizados do Azure AD para o ServiceNow na seção **Mapeamento de atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do ServiceNow em operações de atualização. Se você optar por alterar o [atributo de destino correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)você precisará garantir que a API do ServiceNow suporte a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

10. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para ServiceNow**.

11. Revise os atributos de grupo sincronizados do Azure AD para o ServiceNow na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no ServiceNow para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para ServiceNow, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de propagar ao ServiceNow escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os usuários e grupos definidos no **Escopo** na seção **Configurações.** O ciclo inicial leva mais tempo para ser executado do que os ciclos subseqüentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD esteja sendo executado. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

1. Use os [registros de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e quão perto está da conclusão
3. Se a configuração de provisionamento parecer estar em um estado insalubre, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
* **InvalidLookupReference:** Ao provisionar certos atributos, como Departamento e Localização no ServiceNow, os valores já devem existir em uma tabela de referência no ServiceNow. Por exemplo, você pode ter dois locais (Seattle, Los Angeles) e três departamentos (Sales, Finance, Marketing) na tabela **de nomes de tabela de inserção** no ServiceNow. Se você tentar provisionar um usuário onde seu departamento é "Vendas" e a localização é "Seattle" ele será provisionado com sucesso. Se você tentar provisionar um usuário com o departamento "Vendas" e a localização "LA" o usuário não será provisionado. O local LA deve ser adicionado à tabela de referência no ServiceNow ou o atributo do usuário no Azure AD deve ser atualizado para corresponder ao formato no ServiceNow. 
* **EntradadepropriedadepropriedadesDevalorausenteSAusentes:** Revise os [mapeamentos de atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) para identificar o atributo correspondente. Esse valor deve estar presente no usuário ou grupo que você está tentando prover. 
* Revise a [API ServiceNow SOAP](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) para entender quaisquer requisitos ou limitações (por exemplo, formato para especificar código de país para um usuário)
* Algumas implantações do ServiceNow exigem a permissão de faixas IP para o serviço de provisionamento Ad do Azure. As faixas ip reservadas para o serviço de provisionamento Azure AD podem ser encontradas [aqui](https://www.microsoft.com/download/details.aspx?id=56519) em "AzureActiveDirectoryDomainServices".
* As solicitações de provisionamento são enviadas por padrão para https://{your-instance-name}.service-now.com/{table-name} . Se você precisar de uma URL de inquilino personalizado, você pode fornecer toda a URL no campo nome de ocorrência.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
