---
title: 'Tutorial: Configure workgrid para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Workgrid.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: 94d70447117c73a309959ddf66972c921aa5e687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062798"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Tutorial: Configure workgrid para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Workgrid e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para workgrid.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino da Workgrid](https://www.workgrid.com/)
* Uma conta de usuário no Workgrid com permissões de administração.

## <a name="assigning-users-to-workgrid"></a>Atribuindo usuários ao Workgrid 

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam ter acesso ao Workgrid. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Workgrid seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Dicas importantes para atribuir usuários ao Workgrid 

* Recomenda-se que um único usuário azure AD seja designado ao Workgrid para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Workgrid, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="set-up-workgrid-for-provisioning"></a>Configurar workgrid para provisionamento

Antes de configurar o Workgrid para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento SCIM no Workgrid.

1. Faça login no Workgrid. Navegue **até usuários > provisionamento do usuário**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/user.png)

2. Em **API de gerenciamento de**contas, clique em Criar **credenciais**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/scim.png)

3. Copie os valores de Ponto Final e **Token de Acesso** **SCIM.** Estes serão inseridos no campo **URL do inquilino** e no **token secreto** na guia Provisionamento do aplicativo Workgrid no portal Azure.

    ![Workgrid](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Adicionar Workgrid da galeria

Para configurar o Workgrid para provisionamento automático do usuário com o Azure AD, você precisa adicionar workgrid da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar workgrid na galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Workgrid**, selecione **Workgrid** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Grade de trabalho na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Configuração do provisionamento automático do usuário para workgrid  

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Workgrid com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para workgrid, seguindo as instruções fornecidas no [tutorial de login do Workgrid Single](Workgrid-tutorial.md). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos complementem um ao outro

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Para configurar o provisionamento automático do usuário para workgrid no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workgrid**.

    ![O link Workgrid na lista De Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de Admin, insira os valores de Ponto Final e **Token** de Acesso **scim** recuperados anteriormente em **URL de inquilino** e **Token Secreto,** respectivamente. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Workgrid. Se a conexão falhar, certifique-se de que sua conta workgrid tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para workgrid**.

    ![Mapeamentos de usuários da grade de trabalho](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Workgrid na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Workgrid para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário da grade de trabalho](media/Workgrid-provisioning-tutorial/userattribute.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para workgrid**

    ![Mapeamentos de usuários da grade de trabalho](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Revise os atributos de grupo sincronizados do Azure AD para o Workgrid na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Workgrid para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Mapeamentos de usuários da grade de trabalho](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento Azure AD para Workgrid, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou grupos que você gostaria de provisionar para workgrid escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial leva mais tempo para ser realizado do que as sincronizações subseqüentes. Para obter mais informações sobre quanto tempo levará para os usuários e/ou grupos provisionarem, consulte [quanto tempo levará para provisionar os usuários](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **Status atual** para monitorar o progresso e seguir links para o seu relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Workgrid. Para obter mais informações, consulte [Verifique o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
