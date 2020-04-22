---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TMWS (Trend Micro Web Security) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TMWS (Trend Micro Web Security).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a4c2cddbc9086c80922fcf9c5d96cd197ab4778
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425275"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TMWS (Trend Micro Web Security)

Neste tutorial, você aprenderá a integrar a TMWS (Trend Micro Web Security) ao Azure AD (Azure Active Directory). Ao integrar o TMWS (Trend Micro Web Security) ao Azure AD, você pode:

* No Azure AD, controlar quem tem acesso ao TMWS (Trend Micro Web Security).
* Permitir que os usuários, com as respectivas contas do Azure AD, sejam conectados automaticamente ao TMWS (Trend Micro Web Security).
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do TMWS (Trend Micro Web Security).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O TMWS (Trend Micro Web Security) é compatível com o SSO iniciado por **SP**
* Depois de configurar o TMWS (Trend Micro Web Security), você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Como adicionar o TMWS (Trend Micro Web Security) da galeria

Para configurar a integração do TMWS (Trend Micro Web Security) ao Azure AD, é necessário adicionar o TMWS (Trend Micro Web Security) por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **TMWS (Trend Micro Web Security)** na caixa de pesquisa.
1. Selecione **TMWS (Trend Micro Web Security)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Configurar e testar o logon único do Azure AD para o TMWS (Trend Micro Web Security)

Configure e teste o SSO do Azure AD com o TMWS (Trend Micro Web Security) usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TMWS (Trend Micro Web Security).

Para configurar e testar o SSO do Azure AD com o TMWS (Trend Micro Web Security), conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
    1. **[Definir configurações de sincronização de usuário e de grupo no Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** – Definir configurações de sincronização de usuário e de grupo no Azure AD
1. **[Configurar o SSO do TMWS (Trend Micro Web Security)](#configure-trend-micro-web-security-sso)** – para definir as configurações de logon único no lado do aplicativo.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TMWS (Trend Micro Web Security)** , localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Na caixa de texto **URL de Resposta**, insira uma URL: `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize esse valor com o Identificador real. Contate a [equipe de suporte ao cliente do TMWS (Trend Micro Web Security)](https://success.trendmicro.com/contact-support-north-america) para obter o valor do Identificador. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo do TMWS (Trend Micro Web Security) espera as declarações SAML em um formato específico, que exige que você adicione mapeamentos de atributo personalizado à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo TMWS (Trend Micro Web Security) espera que mais alguns atributos (que são mostrados abaixo) sejam passados novamente na resposta SAML. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome | Atributo de Origem|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o TMWS (Trend Micro Web Security)** , copie as URLs adequado de acordo com seus requisitos.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure, concedendo acesso ao TMWS (Trend Micro Web Security).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TMWS (Trend Micro Web Security)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Definir configurações de sincronização de usuário e de grupo no Azure AD

1. Na navegação à esquerda, clique em **Azure Active Directory**.

1. Em **Gerenciar**, clique em **Registros de aplicativo** e, em seguida, clique em seu novo aplicativo empresarial na área **Todos os aplicativos**.

1. Em **Gerenciar**, clique em **Certificados e segredos**.

1. Na área Segredos do cliente que aparece, clique em **Novo segredo do cliente**.

1. Na tela Adicionar um segredo do cliente que aparece, adicione uma descrição (opcional), selecione um período de validade para esse segredo do cliente e clique em **Adicionar**. O segredo do cliente recém-adicionado aparece sob a área Segredos do cliente.

1. Registre o valor. Posteriormente, você digitará as informações no TMWS.

1. Em **Gerenciar**, clique em **Permissões de API**. 

1. Na tela Permissões da API que é exibida, clique em **Adicionar uma permissão**.

1. Na guia APIs da Microsoft da tela Solicitar permissões de API que é exibida, clique em **Microsoft Graph** e depois em **Permissões de aplicativo**.

1. Localize e adicione as seguintes permissões: 

    * Group.Read.All
    * User.Read.All

1. Clique em **Adicionar permissões**. Uma mensagem é exibida para confirmar que as configurações foram salvas com êxito. As permissões recém-adicionadas aparecem na tela Permissões de API.

1. Na área Fornecer consentimento, clique em **Fornecer o consentimento do administrador para < sua conta de administrador > (diretório padrão)** e depois em **Sim**. Uma mensagem é exibida para confirmar que o consentimento do administrador para as permissões solicitadas foi fornecido com êxito.

1. Clique em **Visão Geral**. 

1. No painel direito que aparece, registre a ID do aplicativo (cliente) e a ID do diretório (locatário). Posteriormente, você digitará as informações no TMWS. Você também pode clicar em **Nomes de domínio personalizados** em Azure **Active Directory > Gerenciar** e registrar o nome de domínio no painel direito.

## <a name="configure-trend-micro-web-security-sso"></a>Configurar o SSO do Trend Micro Web Security

Para configurar o logon único no lado do **TMWS (Trend Micro Web Security)** , é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do TMWS (Trend Micro Web Security)](https://success.trendmicro.com/contact-support-north-america). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

## <a name="test-sso"></a>Testar o SSO 

Depois de você configurar com êxito o serviço do Azure AD e especificar com êxito o Azure AD como o método de autenticação de usuário, você poderá fazer logon no servidor proxy do TMWS para verificar sua configuração. Depois que o logon do Azure AD verificar sua conta, você poderá visitar a Internet.

> [!NOTE]
> O TMWS não dá suporte ao teste de logon único pelo portal do Azure AD, em Visão geral > Logon único > Configurar o logon único com SAML > Teste do seu novo aplicativo empresarial.

1. Limpe todos os cookies do navegador e depois reinicie-o. 

1. Direcione seu navegador para o servidor proxy do TMWS. Para obter mais detalhes, confira [Encaminhamento de tráfego usando arquivos PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Visite qualquer site da Internet. O TMWS direcionará você para o portal cativo do TMWS.

1. Especifique uma conta do Active Directory (formato: domínio\sAMAccountName ou sAMAccountName@domain) ou endereço de email ou nome UPN e clique em **Fazer Logon**. O TMWS envia você para o logon do Azure AD.

1. No logon do Azure AD, digite suas credenciais de conta do AD. Você deverá fazer logon com êxito no TMWS.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o TMWS (Trend Micro Web Security) com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o TMWS (Trend Micro Web Security) com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

