---
title: 'Tutorial: Configurar o Zendesk para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062732"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zendesk para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem realizadas no Zendesk e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e grupos para o Zendesk.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de provisionamento de usuários Azure AD. Para obter informações sobre o que esse serviço faz, como ele funciona e perguntas freqüentes, consulte [Automate provisioning e deprovisionamento de usuários para aplicativos de software como serviço (SaaS) com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tem:

* Um locatário do Azure AD.
* Um inquilino zendesk com o plano Profissional ou melhor habilitado.
* Uma conta de usuário no Zendesk com permissões de administração.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Adicionar Zendesk do Azure Marketplace

Antes de configurar o Zendesk para provisionamento automático do usuário com o Azure AD, adicione o Zendesk do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar zendesk do Marketplace, siga estes passos.

1. No [portal Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Diretório Ativo do Azure](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione ** Novo aplicativo ** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **zendesk** e selecione **Zendesk** no painel de resultados. Para adicionar o aplicativo, **selecione Adicionar**.

    ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Atribuir usuários ao Zendesk

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, decida quais usuários ou grupos no Azure AD precisam acessar o Zendesk. Para atribuir esses usuários ou grupos ao Zendesk, siga as instruções em [Atribuir um usuário ou grupo a um aplicativo corporativo](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Dicas importantes para atribuir usuários ao Zendesk

* Hoje, as funções do Zendesk são preenchidas automaticamente e dinamicamente na UI do portal Azure. Antes de atribuir as funções do Zendesk aos usuários, certifique-se de que uma sincronização inicial seja concluída no Zendesk para recuperar as funções mais recentes no seu inquilino Zendesk.

* Recomendamos que você designe um único usuário Azure AD ao Zendesk para testar sua configuração inicial de provisionamento automático do usuário. Você pode atribuir usuários ou grupos adicionais mais tarde após o sucesso dos testes.

* Ao atribuir um usuário ao Zendesk, selecione qualquer função específica de aplicativo válida, se disponível, na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configure o provisionamento automático do usuário para o Zendesk 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD. Use-o para criar, atualizar e desativar usuários ou grupos no Zendesk com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Você também pode habilitar o login único baseado em SAML para o Zendesk. Siga as instruções no tutorial de login único do [Zendesk](zendesk-tutorial.md). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configure o provisionamento automático do usuário para o Zendesk no Azure AD

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **aplicativos Corporativos** > **Todos os aplicativos** > **Zendesk**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zendesk**.

    ![O link do Zendesk na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Modo de provisionamento Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Na seção Credenciais de **Administração,** insira o nome de usuário do admin, o token secreto e o domínio da sua conta Zendesk. Exemplos desses valores são:

   * Na caixa Nome de usuário do **Admin,** preencha o nome de usuário da conta de administração no seu inquilino zendesk. Um exemplo é admin@contoso.com.

   * Na caixa **de Token Secreto,** preencha o token secreto como descrito no Passo 6.

   * Na caixa **Domínio,** preencha o subdomínio do seu inquilino Zendesk. Por exemplo, para uma conta `https://my-tenant.zendesk.com`com uma URL de inquilino, seu subdomínio é **meu inquilino**.

6. O token secreto para sua conta Zendesk está localizado em**Configurações****de API** > de **administração** > . Certifique-se de que **o Token Access** está definido **como Ativado**.

    ![Configurações de admin do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token secreto zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Depois de preencher as caixas mostradas na Etapa 5, selecione **Conexão de teste** para garantir que o Azure AD possa se conectar ao Zendesk. Se a conexão falhar, certifique-se de que sua conta Zendesk tenha permissões de administração e tente novamente.

    ![Conexão de teste zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Na caixa **E-mail** de notificação, digite o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione **enviar uma notificação de e-mail quando ocorrer uma falha** na caixa de seleção.

    ![E-mail de notificação do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selecione **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Zendesk**.

    ![Sincronização do usuário do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Revise os atributos do usuário sincronizados do Azure AD para o Zendesk na seção Mapeamentos de **atributos.** Os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Zendesk para operações de atualização. Para salvar quaisquer alterações, **selecione Salvar**.

    ![Zendesk correspondendo atributos do usuário](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para o Zendesk**.

    ![Sincronização do grupo Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Revise os atributos de grupo sincronizados do Azure AD para o Zendesk na seção Mapeamentos de **atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder os grupos do Zendesk em operações de atualização. Para salvar quaisquer alterações, **selecione Salvar**.

    ![Atributos do grupo de correspondência zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de escopo, siga as instruções no [tutorial do filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento Azure AD para Zendesk, na seção **Configurações,** altere o **status de provisionamento** para **On**.

    ![Status de provisionamento zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Defina os usuários ou grupos que você deseja prover ao Zendesk. Na seção **Configurações,** selecione os valores desejados no **Escopo**.

    ![Zendesk Scope](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando estiver pronto para provisionar, **selecione Salvar**.

    ![Zendesk Save](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Esta operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **Escopo** na seção **Configurações.** A sincronização inicial leva mais tempo para ser realizado do que sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento Azure AD seja executado. 

Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividade de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento Azure AD no Zendesk.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Zendesk suporta o uso de grupos apenas para usuários com funções **de Agente.** Para obter mais informações, consulte a documentação do [Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando uma função personalizada é atribuída a um usuário ou grupo, o serviço de provisionamento automático do usuário Azure AD também atribui o **agente**de função padrão . Somente Agentes podem ser atribuídos a uma função personalizada. Para obter mais informações, consulte a documentação da API do [Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de contas de usuário para aplicativos corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
