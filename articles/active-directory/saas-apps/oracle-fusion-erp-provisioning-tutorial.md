---
title: 'Tutorial: Configure o Oracle Fusion ERP para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Oracle Fusion ERP.
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061169"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Tutorial: Configure o Oracle Fusion ERP para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Oracle Fusion ERP e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o Oracle Fusion ERP.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os termos gerais de uso do Microsoft Azure para recursos de visualização, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um [inquilino oracle Fusion ERP](https://www.oracle.com/applications/erp/).
* Uma conta de usuário no Oracle Fusion ERP com permissões de administração.

## <a name="assign-users-to-oracle-fusion-erp"></a>Atribuir usuários ao Oracle Fusion ERP 
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam ter acesso ao Oracle Fusion ERP. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Oracle Fusion ERP seguindo as instruções aqui:
 
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Dicas importantes para atribuir usuários ao Oracle Fusion ERP 

 * Recomenda-se que um único usuário Azure AD seja atribuído ao Oracle Fusion ERP para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Oracle Fusion ERP, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a função Default Access são excluídos do provisionamento.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Configure o Oracle Fusion ERP para provisionamento

Antes de configurar o Oracle Fusion ERP para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento SCIM no Oracle Fusion ERP.

1. Faça login no seu [console de admin Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Click the Navigator on the top-left top corner. Em **Ferramentas,** selecione **Console de segurança**.

    ![Oracle Fusion ERP adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Navegue até **Usuários**.
    
    ![Oracle Fusion ERP adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Salve o nome de usuário e a senha da conta de usuário do admin que você usará para fazer login no console de administração Oracle Fusion ERP. Esses valores precisam ser **inseridos** nos campos Nome de Usuário e **Senha** do Administrador na guia Provisionamento do aplicativo Oracle Fusion ERP no portal Azure.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Adicionar Oracle Fusion ERP da galeria

Para configurar o Oracle Fusion ERP para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Oracle Fusion ERP da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Oracle Fusion ERP da galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Oracle Fusion ERP**, selecione **Oracle Fusion ERP** no painel de resultados.

    ![Oracle Fusion ERP na lista de resultados](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Configure o provisionamento automático do usuário para o Oracle Fusion ERP 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Oracle Fusion ERP com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o login único baseado em SAML para o Oracle Fusion ERP seguindo as instruções fornecidas no [tutorial de login único do Oracle Fusion ERP .](oracle-fusion-erp-tutorial.md) O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final scim do Oracle Fusion ERP, consulte a [API REST for Common Features in Oracle Applications Cloud](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para fuze no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Oracle Fusion ERP**.

    ![O link do Oracle Fusion ERP na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` na **URL do inquilino**. Digite o nome de usuário e a senha do admin recuperados anteriormente nos campos Nome de Usuário e **Senha** do **Admin.** Clique em **Testar a conexão** entre o Azure AD e o Oracle Fusion ERP. 

    ![Oracle Fusion ERP adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do Azure Active Directory para Oracle Fusion ERP**.

    ![Oracle Fusion ERP adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Revise os atributos do usuário sincronizados do Azure AD ao Oracle Fusion ERP na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Oracle Fusion ERP para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Oracle Fusion ERP adicionar SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar Grupos de Diretórios Ativos do Azure para o Oracle Fusion ERP**.

    ![Mapeamentos do grupo Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Revise os atributos de grupo sincronizados do Azure AD ao Oracle Fusion ERP na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades correspondentes** são usados para corresponder aos grupos no Oracle Fusion ERP para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do Grupo Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para o Oracle Fusion ERP, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o Oracle Fusion ERP escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Oracle Fusion ERP.

    Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Oracle Fusion ERP só suporta autenticação básica para seu ponto final SCIM.
* O Oracle Fusion ERP não suporta provisionamento de grupo.
* As funções no Oracle Fusion ERP são mapeadas para grupos no Azure AD. Para atribuir funções aos usuários no Oracle Fusion ERP do Azure AD, você precisará atribuir os usuários aos grupos AD desejados do Azure que são nomeados após as funções no Oracle Fusion ERP.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
