---
title: 'Tutorial: Configure o console administrador da Netskope para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Console administrador da Netskope.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061211"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Tutorial: Configure o console administrador da Netskope para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Console Administrador da Netskope e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Console administrador da Netskope.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino do Console administrador de Netskope](https://www.netskope.com/)
* Uma conta de usuário no Console administrador da Netskope com permissões de administração.

## <a name="assigning-users-to-netskope-administrator-console"></a>Atribuindo usuários ao console administrador da Netskope

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Console administrador da Netskope. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Console administrador da Netskope seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Dicas importantes para atribuir usuários ao console administrador da Netskope

* Recomenda-se que um único usuário Azure AD seja designado ao Console administrador da Netskope para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Console administrador da Netskope, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Configurar o console administrador da Netskope para provisionamento

1. Faça login no console [administrador do console netskope](https://netskope.goskope.com/). Navegue até **configurações de > doméstica .**

    ![Console administrador de Netskope Console Console Console](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navegue até **ferramentas**. No menu **Ferramentas,** navegue até **ferramentas de diretório > integração SCIM**.

    ![Ferramentas do console do administrador da Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Console administrador de Netskope adicionar SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Role para baixo e clique no botão **Adicionar token.** Na caixa de diálogo **Adicionar nome do cliente OAuth,** forneça um NOME DO **CLIENTE** e clique no botão **Salvar.**

    ![Console administrador de Netskope adicionar token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Nome client do console do administrador de Netskope](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Copie a **URL do servidor SCIM** e o **TOKEN**. Esses valores serão inseridos nos campos URL do inquilino e token secreto, respectivamente, na guia Provisionamento do aplicativo Console administrador da Netskope no portal Azure.

    ![Console administrador da Netskope criar token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Adicionar console administrador netskope da galeria

Antes de configurar o Console administrador da Netskope para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Console administrador do Netskope da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Console administrador da Netskope na galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite o console administrador da Netskope,** selecione **o console administrador da Netskope** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Console administrador de Netskope na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Configuração do provisionamento automático do usuário para o console administrador da Netskope 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Console administrador da Netskope com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o Console administrador da Netskope, seguindo as instruções fornecidas no tutorial de login único do [administrador da Netskope](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final SCIM do Console administrador da Netskope, consulte [este](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o Console administrador da Netskope no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Netskope Administrator Console**.

    ![O link do console administrador da Netskope na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de **Admin,** o valor de **URL do Servidor SCIM** de entrada recuperado anteriormente na **URL do inquilino**. Insira o valor **TOKEN** recuperado anteriormente no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao console administrador da Netskope. Se a conexão falhar, certifique-se de que a conta do console administrador da Netskope tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para o console administrador do Netskope**.

    ![Mapeamentos de usuários do console do administrador da Netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Console administrador do Netskope na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Console administrador do Netskope para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do console do administrador da Netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar Grupos de diretórios ativos do Azure para o Console administrador do Netskope**.

    ![Mapeamentos do grupo de consoles do administrador da Netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Revise os atributos de grupo sincronizados do Azure AD para o Console administrador do Netskope na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder aos grupos no Console administrador do Netskope para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do grupo de console sumido do administrador da Netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para o Console administrador da Netskope, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o Console administrador da Netskope, escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividadede provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Console administrador da Netskope.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

