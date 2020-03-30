---
title: 'Tutorial: Configure o Diretório Federado para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Diretório Federado.
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
ms.openlocfilehash: 910aaac84dacb75cd76772a0bc2960d9bfa8bb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057884"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Tutorial: Configure o Diretório Federado para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Diretório Federado e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisão e desprovisionamento automático de usuários e/ou grupos para o Diretório Federado.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um Diretório Federado.](https://www.federated.directory/pricing)
* Uma conta de usuário no Diretório Federado com permissões de administração.

## <a name="assign-users-to-federated-directory"></a>Atribuir usuários ao diretório federado
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam ter acesso ao Diretório Federado. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Diretório Federado seguindo as instruções aqui:

 * [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Dicas importantes para atribuir usuários ao Diretório Federado
 * Recomenda-se que um único usuário azure AD seja atribuído ao Diretório Federado para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Diretório Federado, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a função Default Access são excluídos do provisionamento.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Configurar diretório federado para provisionamento

Antes de configurar o Diretório Federado para provisionamento automático do usuário com o Azure AD, você precisará habilitar o provisionamento scim no Diretório Federado.

1. Faça login no seu console de [administração de diretóriofederado](https://federated.directory/of)

    ![Tutorial de Diretório Federado](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navegue até **diretórios > diretórios de usuário** e selecione seu inquilino. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Para gerar um token de portador permanente, navegue até **as chaves de diretório > criar nova chave.** 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated01.png)

4. Crie uma chave de diretório. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Copie o valor de **Token de Acesso**. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Diretório Federado no portal Azure. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Adicionar diretório federado da galeria

Para configurar o Diretório Federado para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Diretório Federado da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar diretório federado da galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Diretório Federado,** **selecione Diretório Federado** no painel de resultados.

    ![Diretório Federado na lista de resultados](common/search-new-app.png)

5. Navegue até a **URL** destacada abaixo em um navegador separado. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Clique **EM LOG IN**.

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Como o Diretório Federado é um aplicativo OpenIDConnect, opte por fazer login no Diretório Federado usando sua conta de trabalho da Microsoft.
    
    ![diretório federado](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Após uma autenticação bem-sucedida, aceite o prompt de consentimento para a página de consentimento. O aplicativo será então adicionado automaticamente ao seu inquilino e você será redirecionado para sua conta do Diretório Federado.

    ![diretório federado Adicionar SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Configuração do provisionamento automático do usuário para o Diretório Federado 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Diretório Federado com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para diretório federado no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de inscrições, **selecione Diretório Federado**.

    ![O link Diretório Federado na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://api.federated.directory/v2/` na URL do inquilino. Insira o valor que você recuperou e salvou anteriormente do Diretório Federado em **Token Secreto**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Diretório Federado. Se a conexão falhar, certifique-se de que sua conta do Diretório Federado tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para o Diretório Federado**.

    ![Tutorial de Diretório Federado](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Revise os atributos do usuário sincronizados do Azure AD para o Diretório Federado na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Diretório Federado para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Tutorial de Diretório Federado](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para Diretório Federado, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de prover ao Diretório Federado escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividadede provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Diretório Federado.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
