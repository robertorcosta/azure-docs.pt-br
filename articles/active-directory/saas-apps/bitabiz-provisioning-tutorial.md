---
title: 'Tutorial: configurar o BitaBIZ para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o BitaBIZ.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d0d38abe-c041-482a-9d3f-ca340678c226
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: ad9176614c4a5235e5138444d4197286204a747f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77059152"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Tutorial: configurar o BitaBIZ para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no BitaBIZ e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no BitaBIZ.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do BitaBIZ](https://bitabiz.dk/en/price/).
* Uma conta de usuário no BitaBIZ com permissões de administrador.

## <a name="assigning-users-to-bitabiz"></a>Atribuindo usuários ao BitaBIZ

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao BitaBIZ. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao BitaBIZ seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Dicas importantes para atribuir usuários ao BitaBIZ

* É recomendável que um único usuário do Azure AD seja atribuído ao BitaBIZ para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao BitaBIZ, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-bitabiz-for-provisioning"></a>Configurar o BitaBIZ para provisionamento

Antes de configurar o BitaBIZ para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no BitaBIZ.

1. Entre no console do [administrador do BitaBIZ](https://www.bitabiz.com/login?lang=en). Clique em **CONFIGURAR ADMIN**.

    ![Console de administração do BitaBIZ](media/bitabiz-provisioning-tutorial/setup-admin.png)

2.  Navegue até **integração**.

    ![Console de administração do BitaBIZ](media/bitabiz-provisioning-tutorial/integration.png)

2.  Navegue até **Microsoft Azure ad provisionamento**.  Selecione **habilitado** no provisionamento automático de usuário. Copie os valores para **URL de ponto de extremidade de provisionamento scim** e **token de portador**. Esses valores serão inseridos nos campos URL do locatário e token secreto na guia provisionamento do aplicativo BitaBIZ no portal do Azure.

    ![BitaBIZ adicionar SCIM](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Adicionar o BitaBIZ da Galeria

Para configurar o BitaBIZ para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o BitaBIZ da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o BitaBIZ da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **BitaBIZ**, selecione **BitaBIZ** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![BitaBIZ na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Configurando o provisionamento automático de usuário para o BitaBIZ 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no BitaBIZ com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o BitaBIZ, seguindo as instruções fornecidas no [tutorial de logon único do BitaBIZ](BitaBIZ-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem uns aos outros

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para BitaBIZ no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **BitaBIZ**.

    ![O link do BitaBIZ na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento](common/provisioning-automatic.png)

5. Na seção credenciais de administrador, insira a **URL do ponto de extremidade de provisionamento scim** e os valores de **token de portador** recuperados anteriormente na URL do locatário e no token secreto, respectivamente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao BitaBIZ. Se a conexão falhar, verifique se sua conta do BitaBIZ tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para BitaBIZ**.

    ![Mapeamentos de usuário BitaBIZ](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o BitaBIZ na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no BitaBIZ para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do BitaBIZ](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o BitaBIZ, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o BitaBIZ escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no BitaBIZ.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* BitaBIZ requer **nome de usuário**, **email**, **nome** e **sobrenome** como atributos obrigatórios. 
* O BitaBIZ não oferece suporte a exclusões rígidas no momento.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
