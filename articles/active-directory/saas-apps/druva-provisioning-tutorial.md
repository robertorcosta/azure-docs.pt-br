---
title: 'Tutorial: configurar o Druva para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Druva.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 9163c64370e0718d6b9acf37bb7a9fc28c936948
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91851145"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Tutorial: configurar o Druva para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Druva e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Druva.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do Druva](https://www.druva.com/products/pricing-plans/).
* Uma conta de usuário no Druva com permissões de administrador.

## <a name="assigning-users-to-druva"></a>Atribuindo usuários ao Druva

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Druva. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Druva seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Dicas importantes para atribuir usuários ao Druva

* É recomendável que um único usuário do Azure AD seja atribuído ao Druva para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Druva, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-druva-for-provisioning"></a>Configurar o Druva para provisionamento

Antes de configurar o Druva para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no Druva.

1. Entre no console do [administrador do Druva](https://console.druva.com). Navegue até **Druva > insincronia**.

    ![Console de administração do Druva](media/druva-provisioning-tutorial/menubar.png)

2. Navegue até **gerenciar**  >  **implantações**  >  **usuários**.

    :::image type="content" source="media/druva-provisioning-tutorial/manage.png" alt-text="Captura de tela do console de administração do Druva. Gerenciar é realçado e o menu gerenciar está visível. Nesse menu, em implantações, os usuários são realçados." border="false":::

3.  Navegue até **configurações**. Clique em **gerar token**.

    :::image type="content" source="media/druva-provisioning-tutorial/settings.png" alt-text="Captura de tela do console de administração do Druva. Gerenciar é realçado e o menu gerenciar está visível. Nesse menu, em implantações, os usuários são realçados." border="false":::

4.  Copie o valor do **token de autenticação** . Esse valor será inserido no campo **token secreto** na guia provisionamento do seu aplicativo Druva no portal do Azure.
    
    :::image type="content" source="media/druva-provisioning-tutorial/auth.png" alt-text="Captura de tela do console de administração do Druva. Gerenciar é realçado e o menu gerenciar está visível. Nesse menu, em implantações, os usuários são realçados." border="false":::

## <a name="add-druva-from-the-gallery"></a>Adicionar o Druva da Galeria

Para configurar o Druva para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Druva da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Druva da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Druva**, selecione **Druva** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Druva na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Configurando o provisionamento automático de usuário para o Druva 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Druva com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Druva, seguindo as instruções fornecidas no [tutorial de logon único do Druva](druva-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Druva no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Druva**.

    ![O link do Druva na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5.  Na seção credenciais de administrador, insira `https://apis.druva.com/insync/scim` a **URL de locatário**. Insira o valor do **token de autenticação** no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Druva. Se a conexão falhar, verifique se sua conta do Druva tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Druva**.

    ![Mapeamentos de usuário Druva](media/druva-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Druva na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Druva para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do Druva](media/druva-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Druva, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o Druva escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Druva.

    Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Limitações do conector

* Druva requer **email** como um atributo obrigatório. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
