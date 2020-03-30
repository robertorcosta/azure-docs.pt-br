---
title: 'Tutorial: Configure Signagelive para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Signagelive.
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
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: bbd0d3c60c5cc4056d5cbadfc7c6d90ae2a37ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063213"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>Tutorial: Configure Signagelive para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Signagelive e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o Signagelive.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino signagelive](https://signagelive.com/pricing/)
* Uma conta de usuário em Signagelive com permissões de administração.

## <a name="assigning-users-to-signagelive"></a>Atribuindo usuários ao Signagelive   

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Signagelive. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Signagelive seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>Dicas importantes para atribuir usuários ao Signagelive   

* Recomenda-se que um único usuário azure AD seja designado ao Signagelive para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Signagelive, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-signagelive--for-provisioning"></a>Configuração Signagelive para provisionamento

Antes de configurar o Signagelive para provisionamento automático do usuário com o Azure AD, você precisará habilitar o provisionamento SCIM no Signagelive.

  Entre em contato com a [Signagelive](mailto:development@signagelive.com) para obter o token secreto necessário para configurar o provisionamento SCIM.

## <a name="add-signagelive-from-the-gallery"></a>Adicionar o Signagelive por meio da galeria

Para configurar o Signagelive para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Signagelive da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Signagelive na galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Signagelive**, selecione **Signagelive** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Signagelive na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Configurando provisionamento automático do usuário para Signagelive    

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Signagelive com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
>  Você também pode optar por ativar o login único baseado em SAML para o Signagelive, seguindo as instruções fornecidas no [tutorial de signon-on do Signagelive Single](Signagelive-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Para configurar o provisionamento automático do usuário para Signagelive no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Signagelive**.

    ![O link do Signagelive na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de ` https://samlapi.signagelive.com/scim/v2` Admin, entrada na **URL do inquilino**. No campo **De Token Secreto,** digite o valor **de Token** do portador fornecido pela equipe de desenvolvimento de engenharia. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Signagelive. Se a conexão falhar, certifique-se de que sua conta Signagelive tenha permissões de administração e tente novamente.
    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para Signagelive**.

    ![Mapeamentos de usuários signagelive](media/signagelive-provisioning-tutorial/usermapping.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Signagelive na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Signagelive para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário Signagelive](media/signagelive-provisioning-tutorial/userattribute.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar O Grupo de Diretórios Ativos do Azure para Signagelive**.

    ![Mapeamentos de usuários signagelive](media/signagelive-provisioning-tutorial/groupmapping.png)

11. Revise os atributos de grupo sincronizados do Azure AD para o Signagelive na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de grupo no Signagelive para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário Signagelive](media/signagelive-provisioning-tutorial/groupattribute.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para Signagelive, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de proprovisionar para O Signagelive escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial leva mais tempo para ser realizado do que as sincronizações subseqüentes. Para obter mais informações sobre quanto tempo levará para os usuários e/ou grupos provisionarem, consulte [quanto tempo levará para provisionar os usuários](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

Você pode usar a seção **Status atual** para monitorar o progresso e seguir links para o seu relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Signagelive. Para obter mais informações, consulte [Verifique o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
