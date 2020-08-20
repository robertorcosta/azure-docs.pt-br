---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Suporte Remoto BeyondTrust | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Suporte Remoto BeyondTrust.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.openlocfilehash: ecaeea625bc4891c95614292a44d31b616d39eb2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549278"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Suporte Remoto BeyondTrust

Neste tutorial, você aprenderá como integrar o suporte remoto BeyondTrust remoto com o Microsoft Azure Active Directory (Azure AD). Ao integrar o suporte remoto BeyondTrust ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso Suporte Remoto BeyondTrust.
* Permitir que os usuários sejam conectados automaticamente ao Suporte Remoto BeyondTrust com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Suporte Remoto BeyondTrust.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Suporte Remoto BeyondTrust é compatível com SSO iniciado por **SP**
* O Suporte Remoto BeyondTrust é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Adicionando o Suporte Remoto BeyondTrust da Galeria

Para configurar a integração do Suporte Remoto BeyondTrust ao Microsoft Azure Active Directory, você precisa adicionar o Suporte Remoto BeyondTrust por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Suporte Remoto BeyondTrust** na caixa de pesquisa.
1. Selecione **Suporte Remoto BeyondTrust** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Configurar e testar logon único do Azure AD para o Suporte Remoto BeyondTrust

Configure e teste o SSO do Azure AD com o Suporte Remoto BeyondTrust usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Suporte Remoto BeyondTrust.

Para configurar e testar o SSO do Azure AD com o Suporte Remoto BeyondTrust, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Suporte Remoto BeyondTrust](#configure-beyondtrust-remote-support-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Suporte Remoto BeyondTrust](#create-beyondtrust-remote-support-test-user)** – para ter um equivalente de Brenda Fernandes no Suporte Remoto BeyondTrust que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Suporte Remoto BeyondTrust**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<HOSTNAME>.bomgar.com/saml`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<HOSTNAME>.bomgar.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Você obterá esses valores, o que será explicado posteriormente no tutorial.

1. O aplicativo de Suporte Remoto BeyondTrust espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo de Suporte Remoto BeyondTrust espera que mais alguns atributos sejam passados novamente na resposta SAML que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de Origem|
    | ---------------| ----------|
    | Givenname | user.givenname |
    | Emailaddress | user.mail |
    | Nome | user.userprincipalname |
    | Nome de Usuário | user.userprincipalname |
    | Grupos | user.groups |
    | Identificador Exclusivo do Usuário | user.userprincipalname |

    > [!NOTE]
    > Ao atribuir grupos do Azure AD para o aplicativo de Suporte Remoto BeyondTrust, a opção ‘Grupos retornados na declaração’ precisará ser modificada de Nenhum para SecurityGroup. Os grupos serão importados para o aplicativo como suas IDs de Objeto. A ID de Objeto do grupo do Azure AD pode ser encontrada verificando as propriedades na interface Azure Active Directory. Isso será necessário para referenciar e atribuir grupos do Azure AD às políticas de grupo corretas.

1. Ao definir o identificador de usuário exclusivo, esse valor deve ser definido como NameID-Format: **Persistente**. Exigimos que este seja um identificador persistente para identificar e associar corretamente o usuário às diretivas de grupo corretas para permissões. Clique no ícone de editar para abrir a caixa de diálogo **Atributos e Declarações do Usuário** para editar o valor do identificador de usuário exclusivo.

1. Na seção **Gerenciar Declaração**, clique em **Escolher formato do identificador de nome** e defina o valor para **Persistente** e clique em **Salvar**.

    ![Atributos e Declarações do Usuário](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar Suporte Remoto BeyondTrust**, copie as URLs adequadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Suporte Remoto BeyondTrust.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Suporte Remoto BeyondTrust**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-beyondtrust-remote-support-sso"></a>Configurar SSO de Suporte Remoto BeyondTrust

1. Em uma janela diferente do navegador da Web, entre no Suporte Remoto BeyondTrust como um administrador.

1. Clique no menu **STATUS** e copie o **Identificador**, **URL de Resposta** e **URL de Logon** e use esses valores na seção **Configuração básica de SAML** no portal do Azure.

    ![Configurar o Suporte Remoto BeyondTrust](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Navegue até a interface de Suporte Remoto BeyondTrust/login em `https://support.example.com/login` no qual **support.example.com** é o nome de host primário do seu dispositivo e autentique usando suas credenciais administrativas.

1. Navegue até **Usuários e Segurança** > **Provedores de Segurança**.

1. No menu suspenso, selecione **SAML** e clique no botão **Criar provedor**.

1. Na seção Configurações do Provedor de Identidade, há uma opção para carregar os Metadados do Provedor de Identidade. Localize o arquivo de XML de metadados que você baixou do portal do Azure e clique no botão **Carregar**. A **ID da Entidade**, **URL de Logon único** e Certificado serão automaticamente carregados, além disso **Associação de Protocolo** precisará ser alterado para **HTTP POST**. Confira a captura de tela abaixo:

    ![Configurar o Suporte Remoto BeyondTrust](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>Criar usuário de teste do Suporte Remoto BeyondTrust

Definiremos as configurações de provisionamento do usuário aqui. Os valores usados nesta seção serão referenciados na seção **Atributos e Declarações do Usuário** no portal do Azure. Configuramos isso como os valores padrão que já foram importados no momento da criação, no entanto, o valor pode ser personalizado, se necessário.

![Criando usuário](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Os atributos de grupos e email não são necessários para essa implementação. Se você estiver utilizando grupos do Azure AD e atribuindo-os a Políticas de Grupo de Suporte Remoto BeyondTrust para permissões, a ID de objeto do grupo precisará ser referenciada por meio de suas propriedades no portal do Azure e colocada na seção ‘grupos disponíveis’. Depois que isso tiver sido concluído, a ID de objeto/grupo do AD agora estará disponível para atribuição a uma política de grupo para permissões.

![Criando usuário](./media/bomgarremotesupport-tutorial/config-user2.png)

![Criando usuário](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Como alternativa, uma política de grupo padrão pode ser definida no provedor de segurança SAML2. Ao definir essa opção, você atribuirá a todos os usuários que autenticam por meio do SAML as permissões especificadas na política de grupo. A política de Membros Gerais está incluída no Suporte Remoto BeyondTrust/Acesso Remoto Privilegiado com permissões limitadas, que podem ser usadas para testar a autenticação e atribuir usuários às políticas corretas. Os usuários não preencherão a lista de usuários do SAML2 por meio de /login > Usuários e Segurança até a primeira tentativa de autenticação bem-sucedida. As informações adicionais em políticas de Grupo podem ser encontradas no link a seguir: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Suporte Remoto BeyondTrust no Painel de Acesso, deve fazer logon automaticamente no Suporte Remoto BeyondTrust para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Suporte Remoto BeyondTrust com o Azure AD](https://aad.portal.azure.com/)
