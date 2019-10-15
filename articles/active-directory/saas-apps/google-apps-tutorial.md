---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao G Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e4449ac3519757bb9670d2d7fec53cb5f3ce152
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948293"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o G Suite

Neste tutorial, você aprenderá a integrar o G Suite ao Microsoft Azure AD (Azure Active Directory). Ao integrar o G Suite ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao G Suite.
* Permitir que os usuários sejam conectados automaticamente ao G Suite com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

- Uma assinatura do Azure AD.
- Uma assinatura do G Suite com SSO (logon único) habilitado.
- Uma assinatura do Google Apps ou uma assinatura do Google Cloud Platform.

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. Este documento foi criado usando o novo usuário, experiência de logon único. Se você ainda estiver usando o antigo, a instalação terá uma aparência diferente. Você pode habilitar a nova experiência nas configurações de logon único do aplicativo do G Suite. Vá para **Azure Active Directory, aplicativos empresariais**, selecione **G Suite**, selecione **Single Sign-on** e, em seguida, clique em **Tentar nossa nova experiência**.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. **P: Essa integração dá suporte para integração de SSO do Google Cloud Platform com o Microsoft Azure AD?**

    R: Sim. O Google Cloud Platform e o Google Apps compartilham a mesma plataforma de autenticação. Portanto, para fazer a integração com o GCP, será necessário configurar o SSO com o Google Apps.

2. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o logon único do AD do AD do Azure?**
  
    R: Sim. Os usuários podem entrar em seus dispositivos Chromebook usando suas credenciais do Azure AD. Consulte este [artigo de suporte do G Suite](https://support.google.com/chrome/a/answer/6060880) para obter informações sobre o motivo dos usuários receberem solicitações de credenciais duas vezes.

3. **P: Se eu habilitar o logon único, os usuários conseguirão usar suas credenciais do Azure AD para entrar em qualquer produto do Google, como Google Sala de aula, GMail, Google Drive, YouTube e assim por diante?**

    R: Sim, dependendo de [qual G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) você quer habilitar ou desabilitar para sua organização.

4. **P: Eu posso habilitar logon único apenas para um subconjunto dos meus usuários do G Suite?**

    R: Não, ativar o logon único exige que todos os usuários do G Suite autentiquem-se imediatamente com as credenciais do Microsoft Azure AD. Como o G Suite não dá suporte a vários provedores de identidade, o provedor de identidade do ambiente do G Suite pode ser o Microsoft Azure AD ou o Google -- mas não os dois ao mesmo tempo.

5. **P: Se um usuário conectar através do Windows, ele será autenticado automaticamente no G Suite sem ser solicitado a fornecer uma senha?**

    R: Há duas opções para este cenário. Primeiro, os usuários podem entrar em dispositivos com Windows 10 por meio do [Ingresso no Active Directory do Azure](../device-management-introduction.md). Como alternativa, os usuários podem entrar em dispositivos com Windows que ingressaram em um domínio para um Active Directory local com logon único habilitado no AD do Azure por meio de uma implantação dos [Serviços de Federação do Active Directory (AD FS)](../hybrid/plan-connect-user-signin.md) . Ambas as opções exigem que você execute as etapas do tutorial a seguir para ativar o logon único entre o Microsoft Azure AD e o G Suite.

6. **P: O que devo fazer quando recebo uma mensagem de erro de "email inválido"?**

    R: Para essa configuração, o atributo de email é necessário para os usuários entrarem. Esse atributo não pode ser definido manualmente.

    O atributo de email é populado automaticamente para qualquer usuário com uma licença válida do Exchange. Se o usuário não estiver habilitado para email, esse erro será recebido, o aplicativo precisa obter esse atributo para fornecer acesso.

    Você pode ir para portal.office.com com uma conta de Administrador, em seguida, clique no Centro de administração, cobrança, assinaturas, selecione sua assinatura do Office 365 e, em seguida, clique em atribuir a usuários, selecione os usuários que você deseja verificar a sua assinatura e no painel direito, clique em editar licenças.

    Depois que a licença do O365 for atribuída, pode levar alguns minutos para ser aplicado. Depois disso, o atributo user.mai será preenchido automaticamente e o problema deve ser resolvido.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O G Suite dá suporte ao SSO iniciado por **SP**

* O G Suite é compatível com o [provisionamento de usuário **automatizado**](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

## <a name="adding-g-suite-from-the-gallery"></a>Adicionar o G Suite da galeria

Para configurar a integração do G Suite ao Microsoft Azure AD, é necessário adicionar o G Suite da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **G Suite** na caixa de pesquisa.
1. Selecione **G Suite** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Configurar e testar logon único do Azure AD para o G Suite

Configure e teste o SSO do Azure AD com o G Suite usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do G Suite.

Para configurar e testar o SSO do Azure AD com o G Suite, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do G Suite](#configure-g-suite-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do G Suite](#create-g-suite-test-user)** – para ter um equivalente de B. Fernandes no G Suite vinculado à representação do usuário no Microsoft Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **G Suite**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje realizar a configuração para o **Gmail**, execute as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

1. Na seção **Configuração Básica do SAML**, caso deseje realizar a configuração para a **Google Cloud Platform**, execute as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. O G Suite não fornece o valor de ID/Identificador da Entidade na configuração de logon único, de modo que quando você desmarcar a opção **emissor específico a um domínio**, o valor do Identificador será `google.com`. Se você marcar a opção **emissor específico a um domínio**, ele será `google.com/a/<yourdomainname.com>`. Para marcar/desmarcar a opção **emissor específico a um domínio**, você precisa ir para a seção **Configurar SSO do G Suite**, que é explicada posteriormente no tutorial. Para obter mais informações, contate a [Equipe de suporte ao cliente do G Suite](https://www.google.com/contact/).

1. Seu aplicativo G Suite espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **Identificador Exclusivo de Usuário** é **user.userprincipalname**, mas o G Suite espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user. mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização.

    ![image](common/edit-attribute.png)

1. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME | Atributo de Origem |
    | ---------------| --------------- |
    | Identificador Exclusivo do Usuário | User.mail |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o G Suite**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela acesso ao G Suite.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **G Suite**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-g-suite-sso"></a>Configurar o SSO do G Suite

1. Abra uma nova guia no navegador e entre no [Console de Admin do G Suite](https://admin.google.com/) usando sua conta de administrador.

2. Clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles** , na parte inferior da tela.

    ![Clique em Segurança.][10]

3. Na página **Segurança**, clique em **Configurar logon único (SSO).**

    ![Clique em SSO.][11]

4. Realize as seguintes alterações de configuração:

    ![Configure o SSO][12]

    a. Selecione **Configurar SSO com um provedor de identidade de terceiros**.

    b. No campo **URL da página de entrada** no G Suite, cole o valor da **URL de logon** que você copiou do portal do Azure.

    c. No campo **URL da página de logoff** no G Suite, cole o valor da **URL de logoff** que você copiou do portal do Azure.

    d. No campo **URL de alteração de senha** do G Suite, cole o valor da **URL de alteração de senha** copiado do Portal do Azure.

    e. No G Suite, para o **Certificado de verificação**, carregue o certificado baixado do Portal do Azure.

    f. Marque/desmarque a opção **Usar um emissor específico a um domínio** de acordo com a observação mencionada na seção **Configuração Básica do SAML** anterior no Azure AD.

    g. Clique em **Salvar Alterações**.

### <a name="create-g-suite-test-user"></a>Criar um usuário de teste do G Suite

O objetivo desta seção é criar um usuário chamado B. Fernandes no software G Suite. O G Suite dá suporte ao provisionamento automático, que é habilitado por padrão. Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no G Suite Software, um novo será criado quando você tentar acessar o G Suite Software.

> [!NOTE]
> Verifique se o usuário já existe no G Suite se o provisionamento no Microsoft Azure Active Directory não foi ativado antes de testar o logon único.

> [!NOTE]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do G Suite no Painel de Acesso, você deverá ser conectado automaticamente ao G Suite no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Configurar Provisionamento de Usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
- [Experimentar o G Suite com o Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png