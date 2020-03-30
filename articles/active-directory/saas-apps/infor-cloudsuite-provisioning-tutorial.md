---
title: 'Tutorial: Configure Infor CloudSuite para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057530"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: Configure Infor CloudSuite para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Infor CloudSuite e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Infor CloudSuite.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino infor CloudSuite](https://www.infor.com/products/infor-os)
* Uma conta de usuário no Infor CloudSuite com permissões de administração.

## <a name="assigning-users-to-infor-cloudsuite"></a>Atribuindo usuários ao Infor CloudSuite

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Infor CloudSuite. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Infor CloudSuite seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Dicas importantes para atribuir usuários ao Infor CloudSuite

* Recomenda-se que um único usuário azure AD seja designado ao Infor CloudSuite para testar a configuração de provisionamento automático do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Infor CloudSuite, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurar o Infor CloudSuite para provisionamento

1. Faça login no seu [console de admin Infor CloudSuite](https://www.infor.com/customer-center). Clique no ícone do usuário e, em seguida, navegue até o **gerenciamento do usuário**.

    ![Infor CloudSuite Admin Console](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Clique no ícone do menu no canto superior esquerdo da tela. Clique em **Gerenciar**.

    ![Infor CloudSuite Adicionar SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navegue até **contas SCIM**.

    ![Infor CloudSuite SCIM Conta](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Adicione um usuário de admin clicando no ícone de mais. Forneça uma **senha SCIM** e digite a mesma senha em **Confirmar senha**. Clique no ícone da pasta para salvar a senha. Em seguida, você verá um **identificador de usuário** gerado para o usuário de admin.

    ![Infor CloudSuite Admin usuário](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite senha](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Identificador Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Para gerar o token do portador, copie o **Identificador de Usuário** e a **Senha SCIM**. Cole-os em bloco de notas++ separados por um cólon. Codifique o valor da seqüência navegando para **Plugins > ferramentas MIME > Encode Basic64**. 

    ![Identificador Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Copie o símbolo do portador. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo Infor CloudSuite no portal Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Adicione Infor CloudSuite da galeria

Antes de configurar o Infor CloudSuite para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Infor CloudSuite da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Infor CloudSuite na galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Infor CloudSuite**, selecione **Infor CloudSuite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Infor CloudSuite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configuração do provisionamento automático do usuário para Infor CloudSuite 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Infor CloudSuite com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o Infor CloudSuite , seguindo as instruções fornecidas no tutorial de login único do [Infor CloudSuite .](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial) O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM do Infor CloudSuite, consulte [isso](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para Infor CloudSuite no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Infor CloudSuite**.

    ![O Infor CloudSuite na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` na **URL do inquilino**. Insira o valor do token do portador recuperado anteriormente em **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Infor CloudSuite. Se a conexão falhar, certifique-se de que sua conta Infor CloudSuite tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do Azure Active Directory para Infor CloudSuite**.

    ![Infor CloudSuite User Mappings](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o Infor CloudSuite na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Infor CloudSuite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Infor CloudSuite Atributos de usuário](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para Infor CloudSuite**.

    ![Mapeamentos do Grupo Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Revise os atributos de grupo sincronizados do Azure AD para o Infor CloudSuite na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no Infor CloudSuite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Infor CloudSuite Group Attributes](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para Infor CloudSuite, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o Infor CloudSuite escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Infor CloudSuite.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)