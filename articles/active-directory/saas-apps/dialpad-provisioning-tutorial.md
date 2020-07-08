---
title: 'Tutorial: configurar o teclado para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o teclado.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058330"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Tutorial: configurar o teclado para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no teclado e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no teclado.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do teclado](https://www.dialpad.com/pricing/).
* Uma conta de usuário no teclado com permissões de administrador.

## <a name="assign-users-to-dialpad"></a>Atribuir usuários ao teclado
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao teclado. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao teclado seguindo as instruções aqui:
 
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Dicas importantes para atribuir usuários ao teclado

 * É recomendável que um único usuário do Azure AD seja atribuído ao teclado para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao teclado, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.

## <a name="setup-dialpad-for-provisioning"></a>Configurar o teclado para provisionamento

Antes de configurar o teclado para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento do teclado.

1. Entre no console do [administrador do teclado](https://dialpadbeta.com/login) e selecione **configurações de administrador**. Verifique se **minha empresa** está selecionada na lista suspensa. Navegue até **autenticação > chaves de API**.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Gere uma nova chave clicando em **Adicionar uma chave** e configurando as propriedades do seu token secreto.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Clique no botão **clique para mostrar valor** para sua chave de API criada recentemente e copie o valor mostrado. Esse valor será inserido no campo **token secreto** na guia provisionamento do seu aplicativo teclado no portal do Azure. 

    ![Teclado criar token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Adicionar o teclado da Galeria

Para configurar o teclado para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o teclado da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o teclado da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **teclado**, selecione **teclado** no painel de resultados.
    ![Teclado na lista de resultados](common/search-new-app.png)

5. Navegue até a **URL** realçada abaixo em um navegador separado. 

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. No canto superior direito, selecione **fazer logon > usar o teclado online**.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Como teclado é um aplicativo OpenIDConnect, opte por fazer logon no teclado usando sua conta corporativa da Microsoft.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento para a página de consentimento. O aplicativo será automaticamente adicionado ao seu locatário e você será redirecionado para sua conta do teclado.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configurar o provisionamento automático de usuário para o teclado

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no teclado com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para teclado no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **teclado**.

    ![O link do teclado na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://dialpad.com/scim` a **URL de locatário**. Insira o valor que você recuperou e salvou anteriormente de teclado no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao teclado. Se a conexão falhar, verifique se sua conta do teclado tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para teclado**.

    ![Mapeamentos de usuário teclado](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o teclado na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no teclado para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do teclado](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o teclado, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o teclado escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no teclado.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Limitações do conector
* Teclado não dá suporte a renomeações de grupo hoje. Isso significa que qualquer alteração no **DisplayName** de um grupo no Azure ad não será atualizada e refletida no teclado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
