---
title: 'Tutorial: configurar o Salesforce para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64de004a1d9b3aa011c447fdded51658582586b0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68825785"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Tutorial: configurar o Salesforce para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Salesforce e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Um locatário do Salesforce.com

> [!IMPORTANT]
> Se você estiver usando uma conta de avaliação do Salesforce.com, não será possível configurar o provisionamento automatizado de usuários. Contas de avaliação não têm o acesso à API necessária habilitado até que sejam compradas. Você pode contornar essa limitação usando uma conta de [desenvolvedor](https://developer.salesforce.com/signup) gratuita para concluir este tutorial.

Se você estiver usando um ambiente de área restrita do Salesforce, consulte o [Tutorial de integração de área restrita do Salesforce](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Como atribuir usuários ao Salesforce

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários ou grupos no Azure AD precisam de acesso ao seu aplicativo Salesforce. Depois de tomar essa decisão, você pode atribuir esses usuários ao seu aplicativo Salesforce seguindo as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Dicas importantes para atribuir usuários ao Salesforce

* Recomendamos a atribuição de um único usuário do Azure AD ao Salesforce para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Salesforce, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento

    > [!NOTE]
    > Este aplicativo importa perfis do Salesforce como parte do processo de provisionamento, que o cliente pode querer selecionar ao atribuir usuários no Azure AD. Observe que os perfis que são importados do Salesforce aparecem como funções no Azure AD.

## <a name="enable-automated-user-provisioning"></a>Habilitar provisionamento automatizado de usuários

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do Salesforce e pela configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas no Salesforce com base na atribuição de usuário e de grupo do Azure AD.

> [!Tip]
> Você também pode optar por habilitar o Logon Único baseado em SAML para o Salesforce, seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-account-provisioning"></a>configurar o provisionamento automático de conta de usuário

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Salesforce.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o Salesforce para logon único, pesquise sua instância do Salesforce usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Salesforce** na galeria de aplicativos. Selecione o Salesforce nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do Salesforce e selecione a guia **Provisionamento**.

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador**, forneça as seguintes configurações:

    a. Na caixa de texto **nome de usuário administrador** , digite um nome de conta do Salesforce que tenha o perfil de **administrador do sistema** em Salesforce.com atribuído.

    b. Na caixa de texto **Senha do Administrador**, digite a senha desta conta.

6. Para obter o token de segurança do Salesforce, abra uma nova guia e entre na mesma conta de administrador do Salesforce. No canto superior direito da página, clique em seu nome e em **configurações**.

    ![Habilitar o provisionamento automático de usuário](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Habilitar o provisionamento automático de usuários.")

7. No painel de navegação esquerdo, clique em **minhas informações pessoais** para expandir a seção relacionada e, em seguida, clique em **Redefinir meu token de segurança**.
  
    ![Habilitar o provisionamento automático de usuário](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Habilitar o provisionamento automático de usuários.")

8. Na página **Redefinir token de segurança** , clique no botão **Redefinir token de segurança** .

    ![Habilitar o provisionamento automático de usuário](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Habilitar o provisionamento automático de usuários.")

9. Marque a caixa de entrada de email associada a essa conta de administrador. Procure um email do Salesforce.com que contenha o novo token de segurança.

10. Copie o token, vá para a janela do Azure AD e cole-o no campo **token secreto** .

11. A **URL do locatário** deverá ser inserida se a instância do Salesforce estiver na nuvem do governo do Salesforce. Caso contrário, é opcional. Insira a URL do locatário usando o formato "https://\<your-Instance \>. my.salesforce.com", substituindo \> da instância de \<your pelo nome da sua instância do Salesforce.

12. No Portal do Azure, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo Salesforce.

13. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

14. Clique em **salvar.**  

15. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Salesforce.**

16. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD para o Salesforce. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Salesforce para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

17. Para habilitar o serviço de provisionamento do Azure AD para o Salesforce, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

18. Clique em **salvar.**

> [!NOTE]
> Depois que os usuários são provisionados no aplicativo Salesforce, o administrador precisa definir as configurações específicas de idioma para eles. Consulte este artigo para obter mais detalhes sobre [a](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) configuração de idioma.

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Salesforce na seção Usuários e Grupos. Observe que a sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Salesforce.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar logon único](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
