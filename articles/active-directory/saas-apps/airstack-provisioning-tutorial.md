---
title: 'Tutorial: Configure o Airstack para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Airstack.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 94738612-b7ab-48c5-a3a9-2c019281aba1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: Zhchia
ms.openlocfilehash: 17736f5215c4ed80a2140cfc664ef76b1a055f79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060377"
---
# <a name="tutorial-configure-airstack-for-automatic-user-provisioning"></a>Tutorial: Configure o Airstack para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Airstack e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o Airstack.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino da Airstack](https://airstack.com/pricing/)
* Uma conta de usuário no Airstack com permissões de administração.

## <a name="assigning-users-to-airstack"></a>Atribuindo usuários ao Airstack

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam ter acesso ao Airstack. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Airstack seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-airstack"></a>Dicas importantes para atribuir usuários ao Airstack

* Recomenda-se que um único usuário azure AD seja designado ao Airstack para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Airstack, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-airstack-for-provisioning"></a>Airstack de configuração para provisionamento

1. Faça login no seu [console de admin Airstack](https://airstack-qa.lenovosoftware.com/). Navegue até **Configurações**.

    ![Airstack Admin Console](media/airstack-provisioning-tutorial/airstackadminmenu.png)

2.  Navegue até **a Azure Config** no menu localizado no lado esquerdo da tela.

    ![Airstack adicionar SCIM](media/airstack-provisioning-tutorial/azureconfig.png)

3.  Clique no botão **Gerar.** Copie o **Token Secreto para O Zure**. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo Airstack no portal Azure.

    ![Airstack criar token](media/airstack-provisioning-tutorial/generatetoken.png)

## <a name="add-airstack-from-the-gallery"></a>Adicionar Airstack da galeria

Antes de configurar o Airstack para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Airstack da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Airstack da galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Airstack**, selecione **Airstack** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Airstack na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-airstack"></a>Configuração do provisionamento automático do usuário para o Airstack 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Airstack com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o Airstack, seguindo as instruções fornecidas no tutorial de login único do [Airstack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos complementem um ao outro

### <a name="to-configure-automatic-user-provisioning-for-airstack-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para Airstack no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Airstack**.

    ![O link Airstack na lista De Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://api-as.lenovosoftware.com/0/as/common/scim` na **URL do inquilino**. Insira o valor **do Token de Autenticação SCIM** recuperado anteriormente no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Airstack. Se a conexão falhar, certifique-se de que sua conta do Airstack tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para o Airstack**.

    ![Mapeamentos de usuários do Airstack](media/airstack-provisioning-tutorial/mappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD ao Airstack na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder às contas de usuário no Airstack para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do airstack](media/airstack-provisioning-tutorial/attributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento Azure AD para Airstack, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar ao Airstack escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Airstack.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

