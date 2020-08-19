---
title: 'Tutorial: configurar a autenticação de identidade da SAP Cloud Platform para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para a autenticação de identidade da SAP Cloud Platform.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c330d8000825b2e0ccbdab8c5c1cdefa4d3d45c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543357"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Tutorial: configurar a autenticação de identidade da SAP Cloud Platform para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas na autenticação de identidade da SAP Cloud Platform e Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para a autenticação de identidade da SAP Cloud Platform.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário de autenticação de identidade da SAP Cloud Platform](https://cloudplatform.sap.com/pricing.html)
* Uma conta de usuário na autenticação de identidade da SAP Cloud Platform com permissões de administrador.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Atribuindo usuários à autenticação de identidade da SAP Cloud Platform

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso à autenticação de identidade da SAP Cloud Platform. Depois de decidir, você pode atribuir esses usuários e/ou grupos à autenticação de identidade da SAP Cloud Platform seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Dicas importantes para atribuir usuários à autenticação de identidade da SAP Cloud Platform

* É recomendável que um único usuário do Azure AD seja atribuído à autenticação de identidade da SAP Cloud Platform para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário à autenticação de identidade da SAP Cloud Platform, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Configurar a autenticação de identidade da SAP Cloud Platform para provisionamento

1. Entre em seu [console de administração de autenticação de identidade da SAP Cloud Platform](https://sapmsftintegration.accounts.ondemand.com/admin). Navegue até **usuários & autorizações > administradores**.

    ![Console de administração de autenticação de identidade da SAP Cloud Platform](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Pressione o botão **+ Adicionar** no painel esquerdo para adicionar um novo administrador à lista. Escolha **adicionar sistema** e insira o nome do sistema.   

> [!NOTE]
> O usuário administrador na autenticação de identidade da SAP Cloud Platform deve ser do tipo **System**. A criação de um usuário administrador normal pode levar a erros *não autorizados* durante o provisionamento.   

3.  Em configurar autorizações, alterne para o botão de alternância em **gerenciar usuários** e **gerenciar grupos**.

    ![Autenticação de identidade da SAP Cloud Platform-adicionar SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Você receberá um email para ativar sua conta e definir uma senha para o **serviço de autenticação de identidade da SAP Cloud Platform**.

4.  Copie a **ID de usuário** e a **senha**. Esses valores serão inseridos nos campos administrador de nome de usuário e senha de administrador, respectivamente, na guia provisionamento do aplicativo de autenticação de identidade da SAP Cloud Platform no portal do Azure.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar Autenticação de Identidade da SAP Cloud Platform por meio da galeria

Antes de configurar a autenticação de identidade da SAP Cloud Platform para o provisionamento automático de usuário com o Azure AD, você precisa adicionar a autenticação de identidade da SAP Cloud Platform da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a autenticação de identidade da SAP Cloud Platform da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **autenticação de identidade da SAP Cloud Platform**, selecione **autenticação de identidade da SAP Cloud Platform** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adicionar SAP Cloud Platform Identity Authentication na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Configurando o provisionamento automático de usuário para a autenticação de identidade da SAP Cloud Platform 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos na autenticação de identidade da SAP Cloud Platform com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para autenticação de identidade da SAP Cloud Platform, seguindo as instruções fornecidas no [tutorial de logon único de autenticação de identidade da SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem uns aos outros

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para a autenticação de identidade da SAP Cloud Platform no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SAP Cloud Platform Identity Authentication**.

    ![Link do SAP Cloud Platform Identity Authentication na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://<tenantID>.accounts.ondemand.com/service/scim ` a **URL de locatário**. Insira os valores de **ID de usuário** e **senha** recuperados anteriormente em **admin username** e **password admin** , respectivamente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar à autenticação de identidade da SAP Cloud Platform. Se a conexão falhar, verifique se sua conta de autenticação de identidade da SAP Cloud Platform tem permissões de administrador e tente novamente.

    ![URL do locatário + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para autenticação de identidade da SAP Cloud Platform**.

    ![Mapeamentos de usuário da autenticação de identidade da SAP Cloud Platform](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para a autenticação de identidade da SAP Cloud Platform na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário na autenticação de identidade da SAP Cloud Platform para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário da autenticação de identidade da SAP Cloud Platform](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para autenticação de identidade da SAP Cloud Platform, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para a autenticação de identidade da SAP Cloud Platform escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure ad na autenticação de identidade da SAP Cloud Platform.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O ponto de extremidade SCIM da autenticação de identidade da SAP Cloud Platform exige que determinados atributos sejam do formato específico. Você pode saber mais sobre esses atributos e seu formato específico [aqui](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

