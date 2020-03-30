---
title: 'Tutorial: Configure o provisionamento automático do usuário figma com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para figma.
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: a50f1c81f5eda78ee6834aba3085f685c197b4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057925"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Tutorial: Configure Figma para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Figma e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para figma.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino figma.](https://www.figma.com/pricing/)
* Uma conta de usuário em Figma com permissões de administração.

## <a name="assign-users-to-figma"></a>Atribuir usuários à Figma.
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam ter acesso ao Figma. Uma vez decidido, você pode atribuir esses usuários e/ou grupos à Figma seguindo as instruções aqui:
 
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Dicas importantes para atribuir usuários à Figma

 * Recomenda-se que um único usuário azure AD seja designado à Figma para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Figma, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a função Default Access são excluídos do provisionamento.

## <a name="set-up-figma-for-provisioning"></a>Configurar figma para provisionamento

Antes de configurar o Figma para provisionamento automático do usuário com o Azure AD, você precisará recuperar algumas informações de provisionamento da Figma.

1. Faça login no seu console de [admin Figma.](https://www.Figma.com/) Clique no ícone de engrenagem ao lado do seu inquilino.

    ![FigmaFigma-provisão de funcionários](media/Figma-provisioning-tutorial/image0.png)

2. Navegue até **as configurações de login**de atualização de > geral .

    ![FigmaFigma-provisão de funcionários](media/Figma-provisioning-tutorial/figma03.png)

3. Copie o **ID do Inquilino**. Esse valor será usado para construir a URL de ponto final SCIM a ser inserida no campo **URL do Inquilino** na guia Provisionamento do aplicativo Figma no portal Azure.

    ![Figma Criar Token](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Role para baixo e clique em **Gerar Token API**.

    ![Figma Criar Token](media/Figma-provisioning-tutorial/token.png)

5. Copie o valor de Token da **API.** Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Figma no portal Azure. 

    ![Figma Criar Token](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Adicione Figma da galeria

Para configurar o Figma para provisionamento automático do usuário com o Azure AD, você precisa adicionar figma da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Figma**, selecione **Figma** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Figma na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Configuração do provisionamento automático do usuário para figma 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos na Figma com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para figma, seguindo as instruções fornecidas no tutorial de login único da [Figma .](figma-tutorial.md) O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Para configurar o provisionamento automático do usuário para Figma no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Figma**.

    ![O link Figma na lista De Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção Credenciais de `https://www.figma.com/scim/v2/<TenantID>` **Admin,** entrada na **URL do inquilino** onde **tenantID** é o valor que você recuperou da Figma anteriormente. Insira o valor **de Token da API** no **Token Secreto**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar à Figma. Se a conexão falhar, certifique-se de que sua conta Figma tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para Figma**.

    ![Mapeamentos de usuários da Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Revise os atributos do usuário sincronizados do Azure AD para o Figma na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Figma para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do Usuário Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para Figma, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de prover ao Figma escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD na Figma.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)