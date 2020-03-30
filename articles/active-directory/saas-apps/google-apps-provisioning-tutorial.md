---
title: 'Tutorial: Configurar o G Suite para provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e descontinuar automaticamente as contas de usuário do Azure AD para o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057695"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: configurar o G Suite para provisionamento automático de usuários

O objetivo deste tutorial é demonstrar as etapas a serem executadas no G Suite e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o G Suite.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> O conector G Suite foi atualizado recentemente em outubro de 2019. As alterações feitas no conector do G Suite incluem:
> - Adicionado suporte para atributos adicionais de usuário e grupo do G Suite. 
> - Nomes de atributo saem do G Suite atualizados para corresponder ao que é definido [aqui](https://developers.google.com/admin-sdk/directory).
> - Mapeamentos de atributos padrão atualizados.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com o G Suite, você precisará dos seguintes itens:

- Um locatário do Azure AD
- [Um inquilino da Suíte G](https://gsuite.google.com/pricing.html)
- Uma conta de usuário em uma Suíte G com permissões de administração.

## <a name="assign-users-to-g-suite"></a>Atribuir usuários ao G Suite

O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o G Suite. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao G Suite seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Dicas importantes para atribuir usuários ao G Suite

* Recomenda-se que um único usuário azure AD seja designado ao G Suite para testar a configuração de provisionamento automático do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao G Suite, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-g-suite-for-provisioning"></a>Configuração G Suite para provisionamento

Antes de configurar o G Suite para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento SCIM no G Suite.

1. Faça login no [console g suite admin](https://admin.google.com/) com sua conta de administrador e, em seguida, selecione **Segurança**. Se você não vê o link, ele pode estar oculto sob o menu **Mais Controles**, na parte inferior da tela.

    ![Selecione segurança.][10]

1. Na página **Segurança**, selecione **Referência de API**.

    ![Selecione Referência de API.][15]

1. Selecione **Habilitar acesso à API**.

    ![Selecione Referência de API.][16]

   > [!IMPORTANT]
   > Para cada usuário que você pretende prover ao G Suite, seu nome de usuário no Azure AD **deve** ser vinculado a um domínio personalizado. Por exemplo, nomes de usuários do tipo bob@contoso.onmicrosoft.com não são aceitos pelo G Suite. Por outro lado, bob@contoso.com é aceito. Você pode alterar o domínio de um usuário existente seguindo as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. Depois de adicionar e verificar os domínios personalizados desejados com o Azure AD, você deve verificá-los novamente com o G Suite. Para verificar domínios no G Suite, consulte as seguintes etapas:

    a. No [Console de Admin do G Suite,](https://admin.google.com/)selecione **Domínios**.

    ![Selecione Domínios][20]

    b. Selecione **Adicionar um domínio ou um alias de domínio**.

    ![Adicione um novo domínio][21]

    c. Selecione **Adicionar outro domínio** e digite o nome do domínio que você deseja adicionar.

    ![Digite o nome de domínio][22]

    d. Selecione **Continuar e confirmar a propriedade do domínio**. Em seguida, siga as etapas para verificar se você possui o nome de domínio. Para obter instruções abrangentes sobre como verificar seu domínio com o Google, consulte [Verificar a propriedade do seu site.](https://support.google.com/webmasters/answer/35179)

    e. Repita as etapas anteriores para quaisquer domínios adicionais que você pretende adicionar ao G Suite.

1. Em seguida, determine qual conta de administração você deseja usar para gerenciar o provisionamento do usuário no G Suite. Navegue até **Funções de Admin**.

    ![Selecione Google Apps][26]

1. Para o **papel de administração** dessa conta, edite os **Privilégios** para esse papel. Verifique se você habilitou todos os **Privilégios de API de Administrador**, de modo que essa conta possa ser usada para o provisionamento.

    ![Selecione Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Adicionar G Suite da galeria

Para configurar o G Suite para provisionamento automático do usuário com o Azure AD, você precisará adicionar o G Suite da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados. 

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

1. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, digite **G Suite**, selecione **G Suite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![G Suite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Configuração do provisionamento automático do usuário para o G Suite 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no G Suite com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o G Suite, seguindo as instruções fornecidas no [tutorial de login único](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)do G Suite . O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

> [!NOTE]
> Para saber mais sobre o ponto final da API do Diretório do G Suite, consulte a [API do diretório](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o G Suite no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **G Suite**.

    ![O link do G Suite na lista de Aplicativos](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

1. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

1. Na seção **Credenciais de Administrador**, selecione **Autorizar**. Isso abre uma caixa de diálogo de autorização do Google em uma nova janela do navegador.

    ![G Suite Autorizar](media/google-apps-provisioning-tutorial/authorize.png)

1. Confirme se deseja dar permissões ao Azure AD para fazer alterações no seu inquilino da G Suite. Selecione **Aceitar**.

    ![Confirme permissões.][28]

1. No Portal do Azure, selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar a seu aplicativo. Se a conexão falhar, verifique se sua conta do G Suite tem permissões de Administrador de Equipe. Em seguida, tente a etapa **Autorizar** novamente.

1. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

1. Clique em **Salvar**.

1. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para o G Suite**.

    ![G Suite User Mappings](media/google-apps-provisioning-tutorial/usermappings.png)

1. Revise os atributos do usuário sincronizados do Azure AD para o G Suite na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no G Suite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do suíte G](media/google-apps-provisioning-tutorial/userattributes.png)

1. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para o G Suite**.

    ![G Suite Group Mappings](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Revise os atributos de grupo sincronizados do Azure AD para o G Suite na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder aos grupos no G Suite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações. A UI exibe o conjunto padrão de mapeamentos de atributos entre o Azure AD e o G Suite. Você pode optar por adicionar atributos adicionais, como Org Unit, clicando em adicionar novo mapeamento.

    ![Atributos do Grupo G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento Azure AD para o G Suite, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

1. Defina os usuários e/ou grupos que você gostaria de provisionar para o G Suite escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no G Suite.

> [!NOTE]
> Se os usuários já tiverem uma conta pessoal/consumidor existente usando o endereço de e-mail do usuário AD do Azure, então isso pode causar algum problema que pode ser resolvido usando a Ferramenta de Transferência do Google antes de realizar a sincronização do diretório.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Problemas comuns
* Falhas de autorização podem ocorrer quando a conta usada para estabelecer uma conexão não é para um administrador no GSuite. Certifique-se de que a conta usada para autorizar o acesso tenha permissões de administração em **todos os domínios** com os que os usuários precisam ser provisionados. 
* O Azure AD suporta desativar usuários no GSuite para que eles não possam acessar o aplicativo, mas ele não exclui usuários no GSuite.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
