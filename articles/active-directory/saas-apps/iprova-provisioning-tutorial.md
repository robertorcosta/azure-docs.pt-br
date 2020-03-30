---
title: 'Tutorial: Configure o iProva para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o iProva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: bb730bad2837616aee0ebfa2da04015542782d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057492"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Tutorial: Configure o iProva para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no iProva e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o iProva.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino do iProva](https://www.iProva.com/)
* Uma conta de usuário no iProva com permissões de administração.

## <a name="assigning-users-to-iprova"></a>Atribuindo usuários ao iProva

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o iProva. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao iProva seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>Dicas importantes para atribuir usuários ao iProva

* Recomenda-se que um único usuário azure AD seja designado ao iProva para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao iProva, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="set-up-iprova-for-provisioning"></a>Configurar o iProva para provisionamento

1. Faça login no seu [console de admin iProva .](https://www.iProva.com/) Navegue até **Ir para > gerenciamento de aplicativos**.

    ![Console de admin iProva](media/iprova-provisioning-tutorial/admin.png)

2.  Clique em **Gerenciamento externo de usuários**.

    ![iProva Adicionar SCIM](media/iprova-provisioning-tutorial/external.png)

3. Para adicionar um novo provedor, clique no ícone **de adição.** Na nova caixa de diálogo **Adicionar provedor,** forneça um **Título**. Você pode optar por adicionar **restrição de acesso baseada em IP**. Clique no botão **OK.**

    ![iProva adicionar novos](media/iprova-provisioning-tutorial/add.png)

    ![provedor de adição do iProva](media/iprova-provisioning-tutorial/addprovider.png)

4.  Clique no botão **de token permanente.** Copie o **token Permanente** e salve-o, pois esta será a única vez que você poderá visualizá-lo. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo iProva no portal Azure.

    ![iProva Criar Token](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>Adicionar o iProva da galeria

Antes de configurar o iProva para provisionamento automático do usuário com o Azure AD, você precisa adicionar o iProva da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o iProva na galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **o iProva**, selecione **o iProva** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![iProva na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>Configuração do provisionamento automático do usuário para o iProva 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no iProva com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o iProva, seguindo as instruções fornecidas no tutorial de login single do [iProva .](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial) O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para iProva no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **iProva**.

    ![O link do iProva na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://identitymanagement.services.iProva.nl/scim` na **URL do inquilino**. Insira o valor **de token permanente** recuperado anteriormente no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao iProva. Se a conexão falhar, certifique-se de que sua conta do iProva tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione Sincronizar usuários do Diretório Ativo do **Azure para o iProva**.

    ![Mapeamentos de usuários do iProva](media/iprova-provisioning-tutorial/usermappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o iProva na seção **Mapeamento de atributos.** Os atributos selecionados como **Propriedades correspondentes** são usados para corresponder às contas de usuário no iProva para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do iProva](media/iprova-provisioning-tutorial/userattributes.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar Grupos de Diretórios Ativos do Azure para iProva**.

    ![Mapeamentos do grupo iProva](media/iprova-provisioning-tutorial/groupmappings.png)

11. Revise os atributos de grupo sincronizados do Azure AD para o iProva na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no iProva para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do Grupo iProva](media/iprova-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para iProva, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de proferir ao iProva escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividadede provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no iProva.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

