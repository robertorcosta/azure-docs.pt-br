---
title: 'Tutorial: Configure o StarLeaf para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o StarLeaf.
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
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064284"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Tutorial: Configure starleaf para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no StarLeaf e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o StarLeaf.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino do StarLeaf.](https://www.starleaf.com/solutions/)
* Uma conta de usuário no StarLeaf com permissões de administração.

## <a name="assign-users-to-starleaf"></a>Atribuir usuários ao StarLeaf
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e grupos no Azure AD precisam ter acesso ao StarLeaf. Em seguida, você pode atribuir os usuários e grupos ao StarLeaf seguindo [estas instruções](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Dicas importantes para atribuir usuários ao StarLeaf

* Recomenda-se que um único usuário azure AD seja designado ao StarLeaf para testar a configuração automática de provisionamento do usuário. Outros usuários e grupos podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao StarLeaf, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a função Default Access são excluídos do provisionamento.

## <a name="set-up-starleaf-for-provisioning"></a>Configure starleaf para provisionamento

Antes de configurar o StarLeaf para provisionamento automático do usuário com o Azure AD, você precisará configurar o provisionamento SCIM no StarLeaf:

1. Faça login no seu [console de admin StarLeaf](https://portal.starleaf.com/#page=login). Navegue até **integrações** > **Adicionar integração**.

    ![StarLeaf adicionar SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Selecione o **Tipo** a ser o Diretório Ativo do Microsoft Azure. Digite um nome adequado em **Nome**. Clique em **Aplicar**.

    ![StarLeaf adicionar SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  Os valores **de URL base scim** e **de token de acesso** serão exibidos em seguida. Esses valores serão inseridos nos campos **URL do inquilino** e **token secreto** na guia Provisionamento do aplicativo StarLeaf no portal Azure. 

    ![StarLeaf criar token](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Adicione StarLeaf da galeria

Para configurar o StarLeaf para provisionamento automático do usuário com o Azure AD, você precisa adicionar o StarLeaf da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o StarLeaf na galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **StarLeaf**, selecione **StarLeaf** no painel de resultados.
    ![StarLeaf na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Configure o provisionamento automático do usuário para o StarLeaf

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no StarLeaf com base em atribuições de usuário e/ou grupo no Azure AD.

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **StarLeaf**.

    ![O link StarLeaf na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de Admin, insira os valores **de URL base scim** e **token de acesso** recuperados anteriormente na URL do **inquilino** e **no Token Secreto,** respectivamente. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao StarLeaf. Se a conexão falhar, certifique-se de que sua conta StarLeaf tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de notificação,** digite o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a **notificação enviar um e-mail quando ocorrer uma** caixa de falha.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para starleaf**.

    ![StarLeaf criar token](media/starleaf-provisioning-tutorial/usermapping.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o StarLeaf na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no StarLeaf para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![StarLeaf criar token](media/starleaf-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Para habilitar o serviço de provisionamento Azure AD para StarLeaf, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar ao StarLeaf escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividadede provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no StarLeaf.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitações do conector

* O StarLeaf não suporta atualmente o provisionamento do grupo. 
* O StarLeaf requer que os valores **de e-mail** e **userName** tenham o mesmo valor de origem.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [revisar registros e obter relatórios sobre atividades de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
