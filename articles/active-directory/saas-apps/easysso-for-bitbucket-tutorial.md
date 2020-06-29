---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EasySSO for BitBucket | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EasySSO for BitBucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ed96e97-b590-4dca-8d00-36288444c641
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1599cf88405594ed61582f359cdd9f8e64fe281
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2020
ms.locfileid: "84764190"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EasySSO for BitBucket

Neste tutorial, você aprenderá a integrar o SSO do EasySSO for BitBucket ao Azure AD (Azure Active Directory). Ao integrar o EasySSO for BitBucket ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao EasySSO for BitBucket.
* Permitir que seus usuários entrem automaticamente no EasySSO for BitBucket com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do EasySSO for BitBucket.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EasySSO for BitBucket dá suporte ao SSO iniciado por **SP e IDP**
* O EasySSO for BitBucket dá suporte ao provisionamento de usuário **Just-In-Time**
* Depois de configurar o EasySSO for BitBucket, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-bitbucket-from-the-gallery"></a>Adicionar o EasySSO for BitBucket da galeria

Para configurar a integração do EasySSO for BitBucket ao Azure AD, é necessário adicionar o EasySSO for BitBucket à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **EasySSO for BitBucket** na caixa de pesquisa.
1. Escolha **EasySSO for BitBucket** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>Configurar e testar o logon único do Azure AD para o EasySSO for BitBucket

Configure e teste o SSO do Azure AD com o EasySSO for BitBucket usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do EasySSO for BitBucket.

Para configurar e testar o SSO do Azure AD com o EasySSO for BitBucket, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do EasySSO for BitBucket](#configure-easysso-for-bitbucket-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do EasySSO for BitBucket](#create-easysso-for-bitbucket-test-user)** – para ter um equivalente de B.Fernandes no EasySSO for BitBucket que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EasySSO for BitBucket**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do EasySSO](mailto:support@techtime.co.nz) para obter esses valores em caso de dúvida. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Seu aplicativo EasySSO for BitBucket espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo EasySSO for BitBucket espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  |  Atributo de Origem|
    | ---------------| --------------- | --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | | user.displayname |
    | urn: oid:2.5.4.4 | | user.surname |
    | urn: oid:2.5.4.42 | | user.givenname |
    
    Caso os usuários do Azure AD tenham o **sAMAccountName** configurado, mapeie **urn:oid:0.9.2342.19200300.100.1.1** para o atributo **sAMAccountName**.
    
1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique nos links **Baixar** do **Certificado (Base64)** ou nas opções de **XML de Metadados de Federação** e salve um deles ou todos eles no computador. Você precisará deles mais tarde para configurar o BitBucket EasySSO.

    ![O link de download do Certificado](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Se você pretende executar a configuração do EasySSO for BitBucket manualmente com o certificado, copie também a **URL de Logon** e o **Identificador do Azure AD** na seção abaixo e salve-os no computador.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao EasySSO for BitBucket.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **EasySSO for BitBucket**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Configurar o SSO do EasySSO for BitBucket

1. Entre em sua instância do Atlassian BitBucket com privilégios de Administrador e navegue até a seção **Administração**. 

    ![Gerenciar Aplicativos](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Na página, localize **EasySSO** e clique nele.

    ![SSO Fácil](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Selecione a opção **SAML**. Isso levará para a seção de configuração do SAML.

    ![SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Selecione a guia **Certificados** na parte superior e você verá a seguinte tela:

    ![URL de metadados](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Agora, localize o **Certificado (Base64)** ou o **Arquivo de Metadados** que você salvou nas etapas anteriores da configuração do **SSO do Azure AD**. Você tem as seguintes opções:

    a. Use o **Arquivo de Metadados** da Federação do Aplicativo que você baixou para o arquivo local no computador. Selecione o botão de opção **Upload** e siga a caixa de diálogo de upload de arquivo específica para seu sistema operacional

    **OR**

    b. Abra o **Arquivo de Metadados** da Federação de Aplicativo para ver o conteúdo (em qualquer editor de texto sem formatação) do arquivo e copie-o na área de transferência. Selecione a opção **Entrada** e cole o conteúdo da área de transferência no campo de texto.
 
    **OR**

    c. Configuração totalmente manual. Abra o **Certificado (Base64)** da Federação de Aplicativo para ver o conteúdo (em qualquer editor de texto sem formatação) do arquivo e copie-o na área de transferência. Cole-o no campo de texto **Certificados de Autenticação de Tokens IdP**. Em seguida, navegue até a guia **Geral** e preencha os campos **URL de Associação POST** e **ID da Entidade** com os respectivos valores da **URL de Logon** e do **Identificador do Azure AD** salvos anteriormente.
 
1. Na parte inferior da página, clique no botão **Salvar**. Você verá que o conteúdo dos arquivos de Metadados ou Certificado é analisado nos campos de configuração. A configuração do EasySSO for BitBucket foi concluída.

1. Para ter a melhor experiência de teste, navegue até a guia **Aparência** e habilite a opção **Botão de Logon SAML**. Isso habilitará o botão separado na tela de logon do BitBucket, especificamente para testar a integração do SAML do Azure AD de ponta a ponta. Você também pode deixar esse botão ligado e configurar seu posicionamento, cor e tradução para o modo de produção.

    ![Aparência](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Se tiver problemas, entre em contato com a [equipe de suporte do EasySSO](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-bitbucket-test-user"></a>Criar um usuário de teste do EasySSO for BitBucket

Nesta seção, é criado um usuário chamado Brenda Fernandes no BitBucket. O EasySSO for BitBucket dá suporte ao provisionamento de usuário Just-In-Time, que fica **desabilitado** por padrão. Para habilitar o provisionamento do usuário, você precisa marcar explicitamente a opção **Criar usuário no logon bem-sucedido** na seção Geral da configuração do plug-in EasySSO. Se um usuário ainda não existir no BitBucket, ele será criado após a autenticação.

No entanto, se você não deseja habilitar o provisionamento automático do usuário no primeiro logon do usuário, os usuários devem existir nos Diretórios de Usuários de back-end que a instância do BitBucket usa, como o LDAP ou o Atlassian Crowd.

![Provisionamento do usuário](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Testar o SSO 

### <a name="idp-initiated-workflow"></a>Fluxo de trabalho iniciado por IdP

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do EasySSO for BitBucket no Painel de Acesso, você deve ser conectado automaticamente à instância do BitBucket na qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="sp-initiated-workflow"></a>Fluxo de trabalho iniciado por SP

Nesta seção, você testará a configuração de logon único do Azure AD usando o botão **Logon do SAML** do BitBucket.

![Logon do SAML do Usuário](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

Este cenário presume que você habilitou o **Botão de Logon do SAML** na guia **Aparência** da página de configuração do BitBucket EasySSO (veja acima). Abra a URL de logon do BitBucket no modo anônimo do navegador para evitar qualquer interferência com as sessões existentes. Clique no botão **Logon do SAML** e você será redirecionado para o fluxo de autenticação do usuário do Azure AD. Após a conclusão com êxito, você será redirecionado novamente para a instância do BitBucket como um usuário autenticado via SAML.

Há uma possibilidade de que você veja a tela a seguir depois de ser redirecionado novamente para o Azure AD

![Tela de falha do EasySSO](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

Nesse caso, você precisará seguir as [instruções desta página]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) para obter acesso ao arquivo **atlassian-bitbucket.log**. Os detalhes do erro estarão disponíveis pela ID de referência encontrada na página de erros do EasySSO.

Caso tenha problemas no resumo das mensagens de log, entre em contato com a [equipe de suporte do EasySSO](mailto:support@techtime.co.nz).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o EasySSO for BitBucket com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o EasySSO for BitBucket com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
