---
title: 'Tutorial: integração do SSO (logon único) do Azure Active Directory ao NetSuite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438498"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>Tutorial: integração do SSO (logon único) do Azure Active Directory ao NetSuite

Neste tutorial, você aprenderá a integrar o NetSuite ao Azure AD (Azure Active Directory). Ao integrar o NetSuite ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao NetSuite.
* Permitir que os usuários sejam conectados automaticamente ao NetSuite com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do NetSuite com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O NetSuite é compatível com SSO iniciado por **IDP**

* O NetSuite é compatível com o provisionamento de usuário **Just-In-Time**

* O NetSuite é compatível com o [provisionamento de usuário automatizado](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-netsuite-from-the-gallery"></a>Adicionar o NetSuite da galeria

Para configurar a integração do NetSuite com o Azure AD, é necessário adicionar o NetSuite da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **NetSuite** na caixa de pesquisa.
1. Selecione **NetSuite** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Configurar e testar o logon único do Azure AD para o NetSuite

Configure e teste o SSO do Azure AD com o NetSuite usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NetSuite.

Para configurar e testar o SSO do Azure AD com o NetSuite, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do NetSuite](#configure-netsuite-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do NetSuite](#create-netsuite-test-user)** – para ter um equivalente de B. Fernandes no NetSuite que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NetSuite**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte ao Cliente do NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo NetSuite espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo NetSuite espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção Declarações de Usuário da caixa de diálogo Atributos de Usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Atributo de Origem | 
    | ---------------| --------------- |
    | conta  | `account id` |

    1. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    1. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    1. Deixe o **Namespace** em branco.

    1. Escolha Origem como **Atributo**.

    1. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    1. Clique em **Ok**

    1. Clique em **Save** (Salvar).

    >[!NOTE]
    >O valor do atributo de conta não é real. Você atualizará esse valor, o que é explicado posteriormente no tutorial.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar NetSuite**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Fernandes a usar o logon único do Azure concedendo acesso ao NetSuite.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **NetSuite**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-netsuite-sso"></a>Configurar o SSO do NetSuite

1. Abra uma nova guia no navegador e entre no site do NetSuite de sua empresa como administrador.

2. Na barra de ferramentas na parte superior da página, clique em **Configuração**, navegue até **Empresa** e clique em **Habilitar Recursos**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na barra de ferramentas no meio da página, clique em **SuiteCloud**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Na seção **Gerenciar Autenticação**, selecione **LOGON ÚNICO DO SAML** para habilitar a opção de LOGON ÚNICO DO DAML no NetSuite.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na barra de ferramentas na parte superior da página, clique em **Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

6. Na lista **TAREFAS DE INSTALAÇÃO**, selecione **Integração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-integration.png)

7. Na seção **GERENCIAR AUTENTICAÇÃO**, clique em **Logon Único do SAML**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-saml.png)

8. Na página de **Configuração do SAML**, na seção **Configuração do NetSuite**, execute as seguintes etapas:

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecione **MÉTODO DE AUTENTICAÇÃO PRIMÁRIA**.

    b. Para o campo rotulado como **METADADOS DO PROVEDOR DE IDENTIDADE DO SAMLV2**, selecione **CARREGAR ARQUIVO DE METADADOS DO IDP**. Em seguida, clique em **Procurar** para carregar o arquivo de metadados baixado no portal do Azure.

    c. Clique em **Enviar**.

9. No NetSuite, clique em **Configuração**, navegue até **Empresa** e clique em **Informações da Empresa** no menu de navegação superior.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-com.png)

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-account-id.png)

    b. Na página **Informações da Empresa**, na coluna à direita, copie a **ID DA CONTA**.

    c. Cole a **ID da Conta** que você copiou da conta do NetSuite no campo **Valor do Atributo** no Azure AD. 

10. Antes que os usuários possam realizar logon único no NetSuite, eles devem primeiro ter as permissões apropriadas no NetSuite. Siga as instruções abaixo para atribuir essas permissões.

    a. No menu de navegação superior, clique em **Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

    b. No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Funções**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Clique em **Nova Função**.

    d. Digite um **Nome** para sua nova função.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-new-role.png)

    e. Clique em **Save** (Salvar).

    f. No menu na parte superior, clique em **Permissões**. Em seguida, clique em **Instalação**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecione **Logon Único do SAML** e, em seguida, clique em **Adicionar**.

    h. Clique em **Save** (Salvar).

    i. No menu de navegação superior, clique em **Instalação** e depois em **Gerenciador de Instalação**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

    j. No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Usuários**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um usuário de teste. Depois, clique em **Editar** e, em seguida, navegue até a guia **Acesso**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Na caixa de diálogo Funções, atribua a função correta que você criou.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-add-role.png)

    m. Clique em **Save** (Salvar).

### <a name="create-netsuite-test-user"></a>Criar um usuário de teste do NetSuite

Nesta seção, um usuário chamado Brenda Fernandes é criado no NetSuite. O NetSuite dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no NetSuite, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do NetSuite no Painel de Acesso, você deverá ser conectado automaticamente ao NetSuite no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o NetSuite com o Azure AD](https://aad.portal.azure.com/)

