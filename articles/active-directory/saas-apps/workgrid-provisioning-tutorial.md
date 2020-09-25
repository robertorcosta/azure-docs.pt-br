---
title: 'Tutorial: configurar o Workgrid para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Workgrid.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: f5f7ed8a905aad873ecfc878979ac32af1e21730
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91312173"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Tutorial: configurar o Workgrid para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Workgrid e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Workgrid.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do Workgrid](https://www.workgrid.com/)
* Uma conta de usuário no Workgrid com permissões de administrador.

## <a name="assigning-users-to-workgrid"></a>Atribuindo usuários ao Workgrid 

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Workgrid. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Workgrid seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Dicas importantes para atribuir usuários ao Workgrid 

* É recomendável que um único usuário do Azure AD seja atribuído ao Workgrid para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Workgrid, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-workgrid-for-provisioning"></a>Configurar o Workgrid para provisionamento

Antes de configurar o Workgrid para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no Workgrid.

1. Faça logon no Workgrid. Navegue até **usuários > provisionamento de usuário**.

    ![Captura de tela da Workgrid U com os usuários e as opções de provisionamento de usuário chamadas.](media/Workgrid-provisioning-tutorial/user.png)

2. Em **API de gerenciamento de contas**, clique em **criar credenciais**.

    ![Captura de tela da seção de gerenciamento de contas A P com a opção Create Credentials chamada out.](media/Workgrid-provisioning-tutorial/scim.png)

3. Copie o **ponto de extremidade scim** e os valores de **token de acesso** . Eles serão inseridos no campo **URL do locatário** e **token secreto** na guia provisionamento do aplicativo Workgrid no portal do Azure.

    ![Captura de tela da seção de gerenciamento de contas A P com o ponto de extremidade C I M e o token de acesso chamados.](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Adicionar o Workgrid da Galeria

Para configurar o Workgrid para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Workgrid da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workgrid da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Workgrid**, selecione **Workgrid** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Workgrid na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Configurando o provisionamento automático de usuário para o Workgrid  

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Workgrid com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Workgrid, seguindo as instruções fornecidas no [tutorial de logon único do Workgrid](Workgrid-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem uns aos outros

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Workgrid no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workgrid**.

    ![O link do Workgrid na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção credenciais de administrador, insira o **ponto de extremidade scim** e os valores de **token de acesso** recuperados anteriormente na **URL do locatário** e no **token secreto** , respectivamente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Workgrid. Se a conexão falhar, verifique se sua conta do Workgrid tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Workgrid**.

    ![Captura de tela da seção mapeamentos com a opção Synchronize Azure Active Directory Users to Workgrid chamada out.](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Workgrid na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Workgrid para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do Workgrid](media/Workgrid-provisioning-tutorial/userattribute.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Workgrid**

    ![Captura de tela da seção mapeamentos com a opção Synchronize Azure Active Directory groups to Workgrid chamada out.](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Examine os atributos de grupo que são sincronizados do Azure AD para o Workgrid na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Workgrid para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Captura de tela da seção mapeamentos de atributo com três mapeamentos exibidos.](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD para o Workgrid, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou grupos que você deseja provisionar para o Workgrid escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo levará para o provisionamento de usuários e/ou grupos, consulte [quanto tempo levará para provisionar usuários](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **status atual** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Workgrid. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
