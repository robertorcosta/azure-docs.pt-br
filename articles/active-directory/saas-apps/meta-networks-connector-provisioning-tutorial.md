---
title: 'Tutorial: Configure o Conector meta networks para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Meta Networks Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061347"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Tutorial: Configure o Conector meta networks para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Meta Networks Connector e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Meta Networks Connector.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino conector de meta redes](https://www.metanetworks.com/)
* Uma conta de usuário no Conector Meta Networks com permissões de administração.

## <a name="assigning-users-to-meta-networks-connector"></a>Atribuindo usuários ao conector Meta Networks

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso a aplicativos selecionados. No contexto do provisionamento automático do usuário, apenas os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático do usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam acessar o Conector de Redes Meta. Uma vez decidido, você pode atribuir esses usuários e/ou grupos ao Meta Networks Connector seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Dicas importantes para atribuir usuários ao Conector meta networks

* Recomenda-se que um único usuário azure AD seja atribuído ao Meta Networks Connector para testar a configuração automática de provisionamento do usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Meta Networks Connector, você deve selecionar qualquer função específica de aplicativo (se disponível) na caixa de diálogo de atribuição. Os usuários com a **função Default Access** são excluídos do provisionamento.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Conector de meta redes de configuração para provisionamento

1. Faça login no seu Console de [Admin do Conector de Redes Meta](https://login.metanetworks.com/login/) usando o nome da organização. Navegue até **as chaves de API de > de administração**.

    ![Meta Networks Connector Admin Console](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Clique no sinal de aferiparaplus no lado superior direito da tela para criar uma nova **tecla DePi**.

    ![Conector de redes meta mais ícone](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Defina o nome da chave da **API** e **a descrição da chave da API**.

    ![Conector de redes meta criar token](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Ativar **privilégios de gravação** para **grupos** e **usuários**.

    ![Privilégios do conector de meta redes](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Clique em **Adicionar**. Copie o **SECRET** e salve-o, pois esta será a única vez que você poderá visualizá-lo. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo Meta Networks Connector no portal Azure.

    ![Conector de redes meta criar token](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Adicione um IdP navegando para **configurações de > de administração > IdP > Criar novo**.

    ![Conector de redes meta adicionar IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Na página **Configuração do IdP,** você pode **nomear** sua configuração de IdP e escolher um **ícone**.

    ![Nome idp do conector de meta redes](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Ícone de IdP do conector de meta redes](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Em **Configurar SCIM,** selecione o nome de chave API criado nas etapas anteriores. Clique em **Salvar**.

    ![Conector de meta redes configurar SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Navegue até **as configurações de > de administração > guia IdP**. Clique no nome da configuração IdP criada nas etapas anteriores para visualizar o **IDI ID**. Este **ID** é adicionado ao fim da URL do **inquilino** enquanto insere o valor no campo URL **do inquilino** na guia Provisionamento do aplicativo Meta Networks Connector no portal Azure.

    ![ID id do conector de meta redes](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Adicionar conector de redes meta da galeria

Antes de configurar o Conector de Redes Meta para provisionamento automático do usuário com o Azure AD, você precisa adicionar o Conector meta networks da galeria de aplicativos Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Conector de Redes Meta da galeria de aplicativos Azure AD, execute as seguintes etapas:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos Enterprise**e selecione Todos **os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Meta Networks Connector**, selecione **Meta Networks Connector** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Meta redes conector na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Configuração do provisionamento automático do usuário para o Conector de Redes Meta 

Esta seção orienta você através das etapas para configurar o serviço de provisionamento Azure AD para criar, atualizar e desativar usuários e/ou grupos no Meta Networks Connector com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por ativar o logon único baseado em SAML para o Conector de Redes Meta, seguindo as instruções fornecidas no [tutorial de logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)do Conector de Redes Meta . O login único pode ser configurado independentemente do provisionamento automático do usuário, embora esses dois recursos complementem um ao outro

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Para configurar o provisionamento automático do usuário para o Conector de Redes Meta no Azure AD:

1. Faça login no [portal Azure](https://portal.azure.com). Selecione **Aplicativos Corporativos**e selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Meta redes conector**.

    ![O link de Meta redes conector na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Admin,** entrada `https://api.metanetworks.com/v1/scim/<IdP ID>` na **URL do inquilino**. Insira o valor **do Token de Autenticação SCIM** recuperado anteriormente no **Secret Token**. Clique **em Conexão de teste** para garantir que o Azure AD possa se conectar ao Conector de Redes Meta. Se a conexão falhar, certifique-se de que a conta meta networks connector tenha permissões de administração e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos,** selecione **Sincronizar usuários do diretório ativo do Azure para meta conector de redes**.

    ![Mapeamentos de usuários do conector de meta-redes](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Revise os atributos do usuário sincronizados do Azure AD para meta Networks Connector na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder às contas de usuário no Meta Networks Connector para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do usuário do conector de meta-redes](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Na seção **Mapeamentos,** selecione **Sincronizar grupos de diretórios ativos do Azure para o conector meta redes**.

    ![Mapeamentos de grupos de conectores de meta-redes](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Revise os atributos de grupo sincronizados do Azure AD para meta Networks Connector na seção **Mapeamento de atributos.** Os atributos selecionados como **propriedades de correspondência** são usados para corresponder aos grupos no Meta Networks Connector para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do grupo conector de meta redes](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento Azure AD para conector de redes meta, altere o **status de provisionamento** para **ativado** na seção **Configurações.**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar ao Conector de Redes Meta escolhendo os valores desejados no **Escopo** na seção **Configurações.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes de Sincronização** para monitorar o progresso e seguir links para o relatório de atividadede provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento Azure AD no Meta Networks Connector.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de contas de usuário para Aplicativos Corporativos](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é acesso ao aplicativo e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

