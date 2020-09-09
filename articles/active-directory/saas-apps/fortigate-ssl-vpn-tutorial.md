---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao FortiGate SSL VPN | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299636"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao FortiGate SSL VPN

Neste tutorial, você aprenderá a integrar o FortiGate SSL VPN ao Azure AD (Azure Active Directory). Ao integrar o FortiGate SSL VPN ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao FortiGate SSL VPN.
* Permitir que os usuários sejam conectados automaticamente ao FortiGate SSL VPN com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do FortiGate SSL VPN habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O FortiGate SSL VPN dá suporte ao SSO iniciado por **SP**
* Depois de configurar o FortiGate SSL VPN, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>Como adicionar o FortiGate SSL VPN por meio da galeria

Para configurar a integração do FortiGate SSL VPN ao Azure AD, você precisará adicionar o FortiGate SSL VPN por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **FortiGate SSL VPN** na caixa de pesquisa.
1. Selecione **FortiGate SSL VPN** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Configurar e testar o SSO do Azure AD para o FortiGate SSL VPN

Configure e teste o SSO do Azure AD com o FortiGate SSL VPN usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do FortiGate SSL VPN.

Para configurar e testar o SSO do Azure AD com o FortiGate SSL VPN, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do FortiGate SSL VPN](#configure-fortigate-ssl-vpn-sso)** : para definir as configurações do logon único no lado do aplicativo.
    1. **Criar um usuário de teste do FortiGate SSL VPN** : para ter um equivalente de B.Fernandes no FortiGate SSL VPN que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-single-sign-on)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **FortiGate SSL VPN**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<FQDN>/remote/login`

    b. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<FQDN>/remote/saml/metadata`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://><FQDN/remote/saml/login`

    d. Na caixa de texto **URL de Logoff**, digite uma URL usando o seguinte padrão: `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, o Identificador, a URL de Resposta e a URL de Logoff reais. Entre em contato com a [equipe de suporte ao cliente do FortiGate SSL VPN](mailto:tac_amer@fortinet.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo FortiGate SSL VPN espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo FortiGate SSL VPN espera que mais alguns atributos sejam transmitidos novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ------------ | --------- |
    | Nome de Usuário | user.userprincipalname |
    | group | user.groups |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o FortiGate SSL VPN**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao FortiGate SSL VPN.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **FortiGate SSL VPN**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-a-security-group-for-the-test-user"></a>Criar um grupo de segurança para o usuário de teste

Nesta seção, você criará um grupo de segurança no Azure Active Directory para o usuário de teste. Esse grupo de segurança será usado pelo FortiGate para conceder acesso à rede do usuário por meio da VPN.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, depois selecione **Grupos**.
1. Selecione **Novo grupo** na parte superior da tela.
1. Nas propriedades do **Novo Grupo**, siga estas etapas:
   1. No campo **Tipo de grupo**, selecione **Segurança**.
   1. No campo **Nome**, insira `FortiGateAccess`.
   1. No campo **Descrição do grupo**, insira `Group for granting FortiGate VPN access`.
   1. Na configuração **As funções do Azure AD podem ser atribuídas ao grupo (Versão prévia)** , selecione **Não**.
   1. No campo **Tipo de associação**, selecione **Atribuído**.
   1. Em **Membros**, selecione **Nenhum membro selecionado**.
   1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
   1. Selecione **Criar**.
1. Depois de retornar para a folha **Grupos** no Azure Active Directory, localize o grupo **Acesso FortiGate** e anote a **ID do objeto** para uso posterior.

## <a name="configure-fortigate-ssl-vpn-sso"></a>Configurar o SSO do FortiGate SSL VPN

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Carregar o certificado SAML Base64 para o dispositivo FortiGate

Depois de concluir a configuração de SAML do aplicativo FortiGate em seu locatário, você baixou o certificado SAML codificado em Base64. Esse certificado precisa ser carregado no dispositivo FortiGate:

1. Entre no portal de gerenciamento do dispositivo FortiGate.
1. No menu à esquerda, clique em **Sistema**.
1. Em **Sistema**, clique em **Certificados**.
1. Clique em **Importar** -> **Certificado Remoto**.
1. Procure o certificado baixado da implantação do aplicativo FortiGate no locatário do Azure, selecione-o e clique em **OK**

Depois que o certificado for carregado, anote o nome dele em **Sistema** > **Certificados** > **Certificado Remoto**. Por padrão, ele será nomeado REMOTE_Cert_**N**, em que **N** é um valor inteiro.

### <a name="perform-fortigate-command-line-configuration"></a>Executar a configuração de linha de comando do FortiGate

As etapas a seguir exigem que a URL de logoff do Azure seja configurada. Essa URL contém um ponto de interrogação (?). Etapas especiais são necessárias para o envio bem-sucedido desse caractere. As etapas não podem ser executadas no console da CLI do FortiGate. Em vez disso, estabeleça uma sessão SSH para o dispositivo FortiGate usando uma ferramenta, como o PuTTY. Se o dispositivo FortiGate for uma máquina virtual do Azure, realize as etapas a seguir no console serial da máquina virtual do Azure.

Para executar essas etapas, você precisará dos valores registrados anteriormente:

- ID da Entidade
- URL de resposta
- URL de logoff
- URL de Logon do Azure
- Identificador do Azure AD
- URL de Logoff do Azure
- Nome do certificado SAML Base64 (REMOTE_Cert_N)

1. Estabeleça uma sessão SSH para seu dispositivo FortiGate e entre com uma conta de administrador do FortiGate.
1. Execute os seguintes comandos:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > A **URL de Logoff do Azure** contém um caractere `?`. É necessário inserir uma sequência de chave especial para fornecer corretamente a URL para o console serial do FortiGate. A URL geralmente é `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
   >
   > Para inserir a URL de logoff do Azure no console serial, digite `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`.
   > 
   > Depois, selecione CTRL+V e cole o restante da URL para completar a linha: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.

### <a name="configure-fortigate-for-group-matching"></a>Configurar o FortiGate para correspondência de grupo

Nesta seção, você vai configurar o FortiGate para reconhecer a ID do objeto do grupo de segurança no qual o usuário de teste reside. Isso permitirá que o FortiGate tome decisões de acesso com base nessa associação de grupo.

Para executar essas etapas, você precisará da ID do objeto do grupo de segurança **FortiGateAccess** criado anteriormente

1. Estabeleça uma sessão SSH para seu dispositivo FortiGate e entre com uma conta de administrador do FortiGate.
1. Execute os seguintes comandos:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>Criar os portais de VPN do FortiGate e a política de firewall

Nesta seção, você configura os portais de VPN do FortiGate e a política de firewall que permitem acesso ao grupo de segurança **FortiGateAccess** criado acima.

Trabalhe com a equipe de suporte do [FortiGate](mailto:tac_amer@fortinet.com) para adicionar os portais de VPN e a política de firewall à plataforma de VPN do FortiGate. Essas etapas precisam ser concluídas antes de você usar o logon único.

## <a name="test-single-sign-on"></a>Testar o logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do FortiGate SSL VPN no Painel de Acesso, você deverá ser conectado automaticamente ao FortiGate SSL VPN, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

A Microsoft e o FortiGate recomendam que você use o cliente de VPN da Fortinet, o FortiClient, para a melhor experiência do usuário final.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o FortiGate SSL VPN com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
