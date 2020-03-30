---
title: 'Tutorial: Configure o SmartFile para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o SmartFile.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b113cc27195b2ce954d677ab0f1ec83e394946be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060211"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Tutorial: Configure o SmartFile para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no SmartFile e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o SmartFile.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um inquilino SmartFile](https://www.SmartFile.com/pricing/).
* Uma conta de usuário no SmartFile com permissões de administração.

## <a name="assigning-users-to-smartfile"></a>Atribuindo usuários ao SmartFile

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o SmartFile. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao SmartFile seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Dicas importantes para atribuir usuários ao SmartFile

* Recomenda-se que um único usuário azure AD seja atribuído ao SmartFile para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao SmartFile, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-smartfile-for-provisioning"></a>Configuração smartFile para provisionamento

Antes de configurar o SmartFile para provisionamento automático do usuário com o Azure AD, você precisará ativar o provisionamento scim no SmartFile e coletar detalhes adicionais necessários.

1. Faça login no seu Console De Dmin SmartFile. Navegue até o canto superior direito do SmartFile Admin Console. Selecione **a tecla do produto**.

    ![SmartFile Admin Console](media/smartfile-provisioning-tutorial/login.png)

2. Para gerar um token do portador, copie a **chave do produto** e a senha do **produto**. Cole-os em um bloco de notas com um cólon entre eles.
    
     ![SmartFile adicionar SCIM](media/smartfile-provisioning-tutorial/auth.png)

    ![SmartFile adicionar SCIM](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Adicionar SmartFile da galeria

Para configurar o SmartFile para provisionamento automático do usuário com o Azure AD, você precisa adicionar o SmartFile da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SmartFile na galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SmartFile**, selecione **SmartFile** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![SmartFile na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Configuração do provisionamento automático do usuário para smartFile 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no SmartFile com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o login único baseado em SAML para o SmartFile, seguindo as instruções fornecidas no [tutorial de login do SmartFile Single](SmartFile-tutorial.md). O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos complementem um ao outro

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para SmartFile no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SmartFile**.

    ![O link do SmartFile na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5.  Na seção **Credenciais de Admin,** entrada `https://<SmartFile sitename>.smartfile.com/ftp/scim` na **URL do inquilino**. Um exemplo se `https://demo1test.smartfile.com/ftp/scim`pareceria. Digite o valor **de token do Portador** (ProductKey:ProductPassword) que você recuperou anteriormente no Secret **Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao SmartFile. Se a conexão falhar, certifique-se de que sua conta SmartFile tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione Sincronizar usuários do diretório ativo do **Azure para smartFile**.

    ![Mapeamentos de usuários do SmartFile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Revise os atributos do usuário sincronizados do Azure AD para o SmartFile na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no SmartFile para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do SmartFile](media/smartfile-provisioning-tutorial/userattribute.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para SmartFile**.

    ![Mapeamentos do Grupo SmartFile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Revise os atributos de grupo sincronizados do Azure AD para o SmartFile na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no SmartFile para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do Grupo SmartFile](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para SmartFile, altere o **status de provisionamento** para **ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o SmartFile escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de sincronização** para monitorar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no SmartFile.

    Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Limitações do conector

* O SmartFile só suporta exclusões duras. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

 [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
