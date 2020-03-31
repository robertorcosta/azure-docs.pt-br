---
title: 'Tutorial: Configure smartsheet para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Smartsheet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063176"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutorial: Configure smartsheet para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Smartsheet e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Smartsheet.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino smartsheet](https://www.smartsheet.com/pricing)
* Uma conta de usuário em um plano Smartsheet Enterprise ou Enterprise Premier com permissões do Administrador do Sistema.

## <a name="assign-users-to-smartsheet"></a>Atribuir usuários ao Smartsheet

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Smartsheet. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Smartsheet seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Dicas importantes para atribuir usuários ao Smartsheet

* Recomenda-se que um único usuário azure AD seja atribuído ao Smartsheet para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Smartsheet, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

* Para garantir a paridade nas atribuições de função do usuário entre o Smartsheet e o Azure AD, recomenda-se utilizar as mesmas atribuições de função preenchidas na lista completa de usuários do Smartsheet. Para recuperar esta lista de usuários do Smartsheet, navegue até **o Administrador da Conta > gerenciamento de > Mais Ações > Download User List (csv)**.

* Para acessar certos recursos no aplicativo, o Smartsheet requer que o usuário tenha várias funções. Para saber mais sobre tipos de usuários e permissões no Smartsheet, vá para [Tipos de Usuário e Permissões](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se um usuário tiver várias funções atribuídas no Smartsheet, você **deve** garantir que essas atribuições de função sejam replicadas no Azure AD para evitar um cenário em que os usuários possam perder acesso a objetos do Smartsheet permanentemente. Cada função única no Smartsheet **DEVE** ser atribuída a um grupo diferente no Azure AD. Em **seguida, o usuário DEVE** ser adicionado a cada um dos grupos correspondentes às funções desejadas. 

## <a name="set-up-smartsheet-for-provisioning"></a>Configurar smartsheet para provisionamento

Antes de configurar o Smartsheet para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento SCIM no Smartsheet.

1. Faça login como um **SysAdmin** no **[portal Smartsheet](https://app.smartsheet.com/b/home)** e navegue até **o Admin da conta**.

    ![Administração da conta smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Vá para **controles de segurança > o provisionamento automático do usuário > editar**.

    ![Controles de segurança do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adicione e valide os domínios de e-mail para os usuários que você planeja provisionar do Azure AD para o Smartsheet. Escolha **Não Habilitado** para garantir que todas as ações de provisionamento sejam originadas apenas do Azure AD e também para garantir que sua lista de usuários do Smartsheet esteja em sincronia com as atribuições do Azure AD.

    ![Provisionamento do usuário smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Uma vez que a validação esteja concluída, você terá que ativar o domínio. 

    ![Domínio de ativação de planilha inteligente](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Gerar o **Token Secreto** necessário para configurar o provisionamento automático do usuário com o Azure AD navegando para **aplicativos e integrações**.

    ![Instalação de smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Escolha **o Acesso à API**. Clique **em Gerar novo token de acesso**.

    ![Instalação de smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Defina o nome do Token de Acesso à API. Clique em **OK**.

    ![Instalação de smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copie o Token de Acesso à API e salve-o, pois esta será a única vez que você poderá visualizá-lo. Isso é necessário no campo **De Token Secreto** em Azure AD.

    ![Token smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Adicionar Smartsheet da galeria

Para configurar o Smartsheet para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Smartsheet da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Smartsheet**, selecione **Smartsheet** no painel de resultados. 

    ![Smartsheet na lista de resultados](common/search-new-app.png)

5. Selecione o botão **''''Inscreva-se' para smartsheet** que irá redirecioná-lo para a página de login do Smartsheet. 

    ![Smartsheet OIDC Adicionar](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Como o Smartsheet é um aplicativo OpenIDConnect, opte por fazer login no Smartsheet usando sua conta de trabalho da Microsoft.

    ![Login do Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Após uma autenticação bem-sucedida, aceite o prompt de consentimento para a página de consentimento. O aplicativo será então adicionado automaticamente ao seu inquilino e você será redirecionado para sua conta smartsheet.

    ![Consentimento oIDc da Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Configure o provisionamento automático do usuário para o Smartsheet 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Smartsheet com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para smartsheet no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Smartsheet**.

    ![O link Smartsheet na lista De Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://scim.smartsheet.com/v2/` na **URL do inquilino**. Insira o valor que você recuperou e salvou anteriormente do Smartsheet no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Smartsheet. Se a conexão falhar, certifique-se de que sua conta Smartsheet tenha permissões SysAdmin e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para smartsheet**.

    ![Mapeamentos de usuários da Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Smartsheet na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Smartsheet para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário da smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento Azure AD para Smartsheet, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar para o Smartsheet escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Smartsheet.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Smartsheet não suporta soft-deletes. Quando o atributo **ativo** de um usuário é definido como False, o Smartsheet exclui o usuário permanentemente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
