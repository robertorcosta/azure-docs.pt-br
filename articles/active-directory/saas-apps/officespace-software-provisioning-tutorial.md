---
title: 'Tutorial: Configure o Software OfficeSpace para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o OfficeSpace Software.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f832a0a6-ad0a-453f-a747-9cd717e11181
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: 3d472b300400cf230773ba01f3f4362988c34e81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063372"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Tutorial: Configure o Software OfficeSpace para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no OfficeSpace Software e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o OfficeSpace Software.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um [inquilino do Software OfficeSpace](https://www.officespacesoftware.com/)
* Uma conta de usuário no OfficeSpace Software com permissões de administração.

## <a name="assigning-users-to-officespace-software"></a>Atribuindo usuários ao Software OfficeSpace

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Software OfficeSpace. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Software OfficeSpace seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Dicas importantes para atribuir usuários ao Software OfficeSpace

* Recomenda-se que um único usuário azure AD seja designado ao OfficeSpace Software para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao OfficeSpace Software, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="set-up-officespace-software-for-provisioning"></a>Configure o Software OfficeSpace para provisionamento

1. Faça login no seu [console de admin do Software OfficeSpace](https://support.officespacesoftware.com/hc). Navegue até **configurações > conectores**.

    ![OfficeSpace Software Admin Console](media/officespace-software-provisioning-tutorial/settings.png)

2.  Navegue até o **SCIM > de sincronização do diretório**.

    ![Software officespace adicionar SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  Copie o **Token de Autenticação SCIM**. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo OfficeSpace Software no portal Azure.

    ![Software do OfficeSpace Criar Token](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Adicione o Software OfficeSpace da galeria

Antes de configurar o Software OfficeSpace para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Software OfficeSpace da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Software OfficeSpace da galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **o OfficeSpace Software,** selecione **o Software OfficeSpace** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![OfficeSpace Software na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Configuração do provisionamento automático do usuário para o Software OfficeSpace 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Software OfficeSpace com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o OfficeSpace Software, seguindo as instruções fornecidas no [tutorial de login único do Software OfficeSpace](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o Software OfficeSpace no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **OfficeSpace Software**.

    ![O link do OfficeSpace Software na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de `https://<subdomain>.officespacesoftware.com/api/scim/v2/` **Admin,** formato de URL de entrada em URL de **inquilino**. Por exemplo, `https://contoso.officespacesoftware.com/api/scim/v2/`. Insira o valor **do Token de Autenticação SCIM** recuperado anteriormente no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Software OfficeSpace. Se a conexão falhar, certifique-se de que sua conta do OfficeSpace Software tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **Notificação enviar um e-mail quando ocorrer uma falha** na caixa de seleção.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do Diretório Ativo do Azure para o Software OfficeSpace**.

    ![Mapeamentos de usuários do software OfficeSpace](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o OfficeSpace Software na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no OfficeSpace Software para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do software OfficeSpace](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento Azure AD para software OfficeSpace, altere o **status de provisionamento** para **ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você gostaria de prover ao Software OfficeSpace, escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no OfficeSpace Software.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

