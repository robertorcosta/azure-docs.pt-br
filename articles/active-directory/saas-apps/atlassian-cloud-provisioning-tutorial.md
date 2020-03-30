---
title: 'Tutorial: Configure a Nuvem Atlassian para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuários para o Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059325"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutorial: Configure a Nuvem Atlassian para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Atlassian Cloud e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o Atlassian Cloud.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino da Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Uma conta de usuário na Atlassian Cloud com permissões de administração.

> [!NOTE]
> A integração de provisionamento azure AD conta com a **API Atlassian Cloud SCIM**, que está disponível para as equipes da Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Adicionar o Atlassian Cloud por meio da galeria

Antes de configurar o Atlassian Cloud para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Atlassian Cloud da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Atlassian Cloud na galeria de aplicativos Do Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Atlassian Cloud**, selecione **Atlassian Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Atlassian Cloud na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Atribuindo usuários ao Atlassian Cloud

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Atlassian Cloud. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Atlassian Cloud seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Dicas importantes para atribuir usuários ao Atlassian Cloud

* Recomenda-se que um único usuário azure AD seja atribuído ao Atlassian Cloud para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Atlassian Cloud, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configuração do provisionamento automático do usuário para o Atlassian Cloud 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos na Atlassian Cloud com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o login único baseado em SAML para o Atlassian Cloud, seguindo as instruções fornecidas no tutorial de login único da [Atlassian Cloud](atlassian-cloud-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o Atlassian Cloud no Azure AD:

1. Faça login no [portal Do Zure](https://portal.azure.com) e selecione **Aplicativos Corporativos,** selecione **Todos os aplicativos**e selecione **Atlassian Cloud**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Atlassian Cloud**.

    ![O link para o Atlassian Cloud na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Navegue até [o gerente de organização da Atlassian](https://admin.atlassian.com)> **selecione o diretório de > de org**.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Clique **em Provisionamento de usuário** e clique em Criar um **diretório**. Copie a **URL base do diretório** e o **Token portador** para os campos URL e **Token Secreto** do **Inquilino,** respectivamente.

    ![Provisionamento](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![em nuvem atlassian Atlassian Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Na seção Credenciais de **Administração,** insira a **URL do inquilino** e o Sinal **Secreto** da conta da sua Atlassian Cloud. Exemplos desses valores são:

   * No campo URL do **inquilino,** preencha o ponto final do inquilino específico que você recebe do Atlassian, conforme descrito na Etapa 6. Por exemplo: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * No campo **do Token Secreto,** preencha o token secreto como descrito no Passo 6.

8. Ao preencher os campos mostrados no Passo 7, clique em **Conexão de teste** para garantir que o Azure AD possa se conectar à Nuvem Atlassian. Se a conexão falhar, certifique-se de que sua conta atlassian Cloud tenha permissões de administração e tente novamente.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Clique em **Salvar**.

11. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para o Atlassian Cloud**.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Revise os atributos do usuário sincronizados do Azure AD para o Atlassian Cloud na seção **Mapeamento de atributos.** Os atributos selecionados como **Propriedades correspondentes** são usados para corresponder às contas de usuário no Atlassian Cloud para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para a Nuvem Atlassian**.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Revise os atributos de grupo sincronizados do Azure AD para o Atlassian Cloud na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no Atlassian Cloud para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Para habilitar o serviço de provisionamento Azure AD para o Atlassian Cloud, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Defina os usuários e/ou grupos que você gostaria de provisionar para o Atlassian Cloud escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento em nuvem atlassiano](./media/atlassian-cloud-provisioning-tutorial/save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD na Atlassian Cloud.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Atlassian Cloud permite o provisionamento de usuários somente a partir de [domínios verificados.](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)
* A Atlassian Cloud não suporta renomes de grupo hoje. Isso significa que quaisquer alterações no displayNome de um grupo no Azure AD não serão atualizadas e refletidas na Nuvem Atlassian.
* O valor do atributo do usuário de **e-mail** no Azure AD só é preenchido se o usuário tiver uma caixa de correio do Microsoft Exchange. Se o usuário não tiver um, recomenda-se mapear um atributo diferente desejado ao atributo **de e-mails** na Nuvem Atlassian.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png