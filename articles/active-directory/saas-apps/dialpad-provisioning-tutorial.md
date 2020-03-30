---
title: 'Tutorial: Configure dialpad para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Dialpad.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058330"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Tutorial: Configure dialpad para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Dialpad e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para dialpad.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino do Dialpad.](https://www.dialpad.com/pricing/)
* Uma conta de usuário no Dialpad com permissões de administração.

## <a name="assign-users-to-dialpad"></a>Atribuir usuários ao Dialpad
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Dialpad. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Dialpad seguindo as instruções aqui:
 
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Dicas importantes para atribuir usuários ao Dialpad

 * Recomenda-se que um único usuário azure AD seja atribuído ao Dialpad para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Dialpad, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a função Default Access são excluídos do provisionamento.

## <a name="setup-dialpad-for-provisioning"></a>Dialpad de configuração para provisionamento

Antes de configurar o Dialpad para provisionamento automático do usuário com o Azure AD, você precisará recuperar algumas informações de provisionamento do Dialpad.

1. Faça login no [console de dumin dialpad](https://dialpadbeta.com/login) e selecione **configurações de admin**. Certifique-se de que **minha empresa** está selecionada a partir da lista de itens. Navegue até **as teclas de Autenticação > API**.

    ![Dialpad adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Gere uma nova tecla clicando em **Adicionar uma chave** e configurando as propriedades do seu token secreto.

    ![Dialpad adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Clique no **botão Clique para mostrar valor** para sua chave API recentemente criada e copie o valor mostrado. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Dialpad no portal Azure. 

    ![Dialpad Criar Token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Adicionar Dialpad da galeria

Para configurar o Dialpad para provisionamento automático do usuário com o Azure AD, você precisa adicionar dialpad da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Dialpad na galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Dialpad**, selecione **Dialpad** no painel de resultados.
    ![Dialpad na lista de resultados](common/search-new-app.png)

5. Navegue até a **URL** destacada abaixo em um navegador separado. 

    ![Dialpad adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. No canto superior direito, selecione **Log In > Usar Dialpad on-line**.

    ![Dialpad adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Como o Dialpad é um aplicativo OpenIDConnect, opte por fazer login no Dialpad usando sua conta de trabalho da Microsoft.

    ![Dialpad adicionar SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Após uma autenticação bem-sucedida, aceite o prompt de consentimento para a página de consentimento. O aplicativo será então adicionado automaticamente ao seu inquilino e você será redirecionado para sua conta dialpad.

    ![Dialpad adicionar SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configure o provisionamento automático do usuário para dialpad

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Dialpad com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para dialpad no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Dialpad**.

    ![O link Dialpad na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://dialpad.com/scim` na **URL do inquilino**. Insira o valor que você recuperou e salvou anteriormente do Dialpad em **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Dialpad. Se a conexão falhar, certifique-se de que a conta dialpad tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione Sincronizar usuários do diretório ativo do **Azure para Dialpad**.

    ![Mapeamentos de usuários do Dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Dialpad na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Dialpad para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do Dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento Azure AD para Dialpad, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar para o Dialpad escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Dialpad.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Limitações do conector
* Dialpad não suporta renomes de grupo hoje. Isso significa que quaisquer alterações no **displayNome** de um grupo no Azure AD não serão atualizadas e refletidas no Dialpad.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
