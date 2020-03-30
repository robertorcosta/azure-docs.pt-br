---
title: 'Tutorial: Configure flock para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Flock.
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
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: cd7aae05b064657c7b9072402f4bc4d4d7fef7a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057849"
---
# <a name="tutorial-configure-flock-for-automatic-user-provisioning"></a>Tutorial: Configure Flock para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Flock and Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o Flock.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino do Flock](https://flock.com/pricing/)
* Uma conta de usuário em Flock com permissões de administração.

## <a name="assigning-users-to-flock"></a>Atribuindo usuários ao Flock 

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam ter acesso ao Flock. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Flock seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-flock"></a>Dicas importantes para atribuir usuários ao Flock 

* Recomenda-se que um único usuário azure AD seja designado ao Flock para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Flock, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-flock--for-provisioning"></a>Rebanho de configuração para provisionamento

Antes de configurar o Flock para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento SCIM no Flock.

1. Faça login em [Flock](https://web.flock.com/?). Clique **em 'Configurações' Ícone** > **Gerenciar sua equipe**.

    ![Flock](media/flock-provisioning-tutorial/icon.png)

2. Selecione **Auth e Provisionamento**.

    ![Flock](media/Flock-provisioning-tutorial/auth.png)

3. Copie o **Token de API**. Esses valores serão inseridos no campo **Token Secreto** na guia Provisionamento do aplicativo Flock no portal Azure.

    ![Flock](media/Flock-provisioning-tutorial/provisioning.png)


## <a name="add-flock--from-the-gallery"></a>Adicionar Flock da galeria

Para configurar o Flock para provisionamento automático do usuário com o Azure AD, você precisa adicionar flock da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar flock da galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite 'Rebanho** **'''Rebanho'** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Rebanho na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-flock"></a>Configuração do provisionamento automático do usuário para O Rebanho  

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Flock com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o Flock, seguindo as instruções fornecidas no [tutorial de login do Flock Single](Flock-tutorial.md). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos complementem um ao outro

### <a name="to-configure-automatic-user-provisioning-for-flock--in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o Flock no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Flock**.

    ![O link 'Rebanho' na lista De aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de Admin, `https://api.flock-staging.com/v2/scim` insira os valores de Token e **API** recuperados anteriormente em URL de **inquilino** e **Token Secreto,** respectivamente. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Flock. Se a conexão falhar, certifique-se de que sua conta do Flock tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários de diretório ativo do Azure para rebanho**.

    ![Mapeamentos de usuários de rebanho](media/flock-provisioning-tutorial/usermapping.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Flock na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Flock para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do Usuário do Rebanho](media/flock-provisioning-tutorial/userattribute.png)

11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento Azure AD para Flock, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você gostaria de prover ao Flock escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial leva mais tempo para ser realizado do que as sincronizações subseqüentes. Para obter mais informações sobre quanto tempo levará para os usuários e/ou grupos provisionarem, consulte [quanto tempo levará para provisionar os usuários](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **Status atual** para monitorar o progresso e seguir links para o seu relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Flock. Para obter mais informações, consulte [Verifique o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).



## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)