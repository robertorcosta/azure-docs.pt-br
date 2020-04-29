---
title: 'Tutorial: configurar o SmartSheet para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o SmartSheet.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063176"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutorial: configurar o SmartSheet para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no SmartSheet e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no SmartSheet.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do SmartSheet](https://www.smartsheet.com/pricing)
* Uma conta de usuário em um plano do SmartSheet Enterprise ou Enterprise Premier com permissões de administrador do sistema.

## <a name="assign-users-to-smartsheet"></a>Atribuir usuários ao SmartSheet

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao SmartSheet. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao SmartSheet seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Dicas importantes para atribuir usuários ao SmartSheet

* É recomendável que um único usuário do Azure AD seja atribuído ao SmartSheet para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao SmartSheet, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

* Para garantir a paridade nas atribuições de função de usuário entre o SmartSheet e o Azure AD, é recomendável utilizar as mesmas atribuições de função preenchidas na lista completa de usuários do SmartSheet. Para recuperar essa lista de usuários do SmartSheet, navegue até **administrador da conta > gerenciamento de usuários > mais ações > baixar a lista de usuários (CSV)**.

* Para acessar determinados recursos no aplicativo, o SmartSheet exige que um usuário tenha várias funções. Para saber mais sobre tipos de usuário e permissões no SmartSheet, vá para [tipos de usuário e permissões](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se um usuário tiver várias funções atribuídas no SmartSheet, você **deverá** garantir que essas atribuições de função sejam replicadas no Azure ad para evitar um cenário em que os usuários possam perder o acesso a objetos SmartSheet permanentemente. Cada função exclusiva em SmartSheet **deve** ser atribuída a um grupo diferente no Azure AD. O usuário **deve** então ser adicionado a cada um dos grupos correspondentes às funções desejadas. 

## <a name="set-up-smartsheet-for-provisioning"></a>Configurar o SmartSheet para provisionamento

Antes de configurar o SmartSheet para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no SmartSheet.

1. Entre como um **sysadmin** no portal do **[SmartSheet](https://app.smartsheet.com/b/home)** e navegue até **administrador da conta**.

    ![Administrador da conta do SmartSheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Vá para **controles de segurança > provisionamento automático do usuário > editar**.

    ![Controles de segurança SmartSheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adicione e valide os domínios de email para os usuários que você planeja provisionar do Azure AD para o SmartSheet. Escolha **não habilitado** para garantir que todas as ações de provisionamento sejam originadas somente do Azure AD e também para garantir que sua lista de usuários do SmartSheet esteja em sincronia com as atribuições do Azure AD.

    ![Provisionamento de usuário do SmartSheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Depois que a validação for concluída, você precisará ativar o domínio. 

    ![SmartSheet ativar domínio](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Gere o **token secreto** necessário para configurar o provisionamento automático de usuário com o Azure ad navegando para **aplicativos e integrações**.

    ![SmartSheet instalar](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Escolha **acesso à API**. Clique em **gerar novo token de acesso**.

    ![SmartSheet instalar](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Defina o nome do token de acesso à API. Clique em **OK**.

    ![SmartSheet instalar](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copie o token de acesso da API e salve-o, pois esse será o único momento que você pode exibi-lo. Isso é necessário no campo **token secreto** no Azure AD.

    ![Token SmartSheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Adicionar o SmartSheet da Galeria

Para configurar o SmartSheet para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o SmartSheet da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SmartSheet**, selecione **SmartSheet** no painel de resultados. 

    ![SmartSheet na lista de resultados](common/search-new-app.png)

5. Selecione o botão **inscrever-se no SmartSheet** , que o redirecionará para a página de logon do SmartSheet. 

    ![SmartSheet OIDC Add](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Como SmartSheet é um aplicativo OpenIDConnect, opte por fazer logon no SmartSheet usando sua conta corporativa da Microsoft.

    ![SmartSheet OIDC logon](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento para a página de consentimento. O aplicativo será automaticamente adicionado ao seu locatário e você será redirecionado para sua conta do SmartSheet.

    ![Consentimento SmartSheet OIDc](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Configurar o provisionamento automático de usuário para o SmartSheet 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no SmartSheet com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para SmartSheet no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SmartSheet**.

    ![O link do SmartSheet na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://scim.smartsheet.com/v2/` a **URL de locatário**. Insira o valor que você recuperou e salvou anteriormente de SmartSheet no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao SmartSheet. Se a conexão falhar, verifique se sua conta do SmartSheet tem permissões SysAdmin e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para SmartSheet**.

    ![Mapeamentos de usuário SmartSheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o SmartSheet na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no SmartSheet para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do SmartSheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o SmartSheet, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o SmartSheet escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no SmartSheet.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* SmartSheet não dá suporte a exclusões reversível. Quando o atributo **ativo** de um usuário é definido como false, o SmartSheet exclui o usuário permanentemente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
