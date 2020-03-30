---
title: Registre um aplicativo web que assina em usuários - plataforma de identidade da Microsoft | Azure
description: Saiba como cadastrar um aplicativo web que assina em usuários
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701562"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Web app que assina em usuários: Registro de aplicativos

Este artigo explica as especificidades de registro do aplicativo para um aplicativo web que assina em usuários.

Para registrar sua inscrição, você pode usar:

- O [aplicativo web começa rapidamente](#register-an-app-by-using-the-quickstarts). Além de ser uma ótima primeira experiência com a criação de um aplicativo, quickstarts no portal Azure contêm um botão chamado **Faça essa alteração para mim**. Você pode usar este botão para definir as propriedades que você precisa, mesmo para um aplicativo existente. Você precisará adaptar os valores dessas propriedades ao seu próprio caso. Em particular, a URL da API web para o seu aplicativo provavelmente será diferente do padrão proposto, o que também afetará o URI de saída de saída.
- O portal Azure para [registrar sua aplicação manualmente](#register-an-app-by-using-the-azure-portal).
- PowerShell e ferramentas de linha de comando.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registre um aplicativo usando o quickstarts

Você pode usar esses links para inicializar a criação do seu aplicativo web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registre um aplicativo usando o portal Azure

> [!NOTE]
> O portal a ser usado é diferente dependendo se seu aplicativo é executado na nuvem pública do Microsoft Azure ou em uma nuvem nacional ou soberana. Para obter mais informações, consulte [Nuvens Nacionais](./authentication-national-cloud.md#app-registration-endpoints).


1. Faça login no [portal do Azure](https://portal.azure.com) usando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft. Alternativamente, faça login no portal azure de escolha para a nuvem nacional.
1. Se sua conta lhe der acesso a mais de um inquilino, selecione sua conta no canto superior direito. Em seguida, defina sua sessão de portal para o inquilino desejado do Azure Active Directory (Azure AD).
1. No painel esquerdo, selecione o serviço **azure Active Directory** e, em seguida, selecione **Registros** > do aplicativo**Novo registro**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
   1. Escolha os tipos de conta suportadas para sua aplicação. (Consulte [os tipos de conta suportados](./v2-supported-account-types.md).)
   1. Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo. Por exemplo, **digite AspNetCore-WebApp**.
   1. Para **redirecionar uri**, adicione o tipo de aplicativo e o destino URI que aceitará respostas de token retornadas após autenticação bem-sucedida. Por exemplo, **https://localhost:44321**digite . Em seguida, selecione **Registrar**.
1. Selecione o menu **Autenticação** e, em seguida, adicione as seguintes informações:
   1. Para url de **https://localhost:44321/signin-oidc** **resposta,** adicione o tipo **Web**.
   1. Na seção **Configurações avançadas**, defina **URL de Logoff** como **https://localhost:44321/signout-oidc**.
   1. Em **Concessão implícita**, selecione **Tokens de ID**.
   1. Selecione **Salvar**.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Quando a **página registrar uma página de aplicativo** for exibida, digite as informações de registro do seu aplicativo:
   1. Escolha os tipos de conta suportadas para sua aplicação. (Consulte [os tipos de conta suportados](./v2-supported-account-types.md).)
   1. Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo. Por exemplo, digite **MailApp-openidconnect-v2**.
   1. Na seção **Redirecionar URI (opcional),** selecione **Web** na caixa de **https://localhost:44326/** combinação e digite o seguinte uri de redirecionamento: .
1. Selecione **Registrar** para criar o aplicativo.
1. Selecione o menu **Autenticação.**
1. Na seção Subvenção implícita **de configurações avançadas,** | **Implicit grant** selecione **Tokens De ID**. Esta amostra requer que o [fluxo de subvenção implícito](v2-oauth2-implicit-grant-flow.md) seja habilitado para entrar no usuário.
1. Selecione **Salvar**.

# <a name="java"></a>[Java](#tab/java)

1. Quando a **página Registrar uma página de aplicativo** for exibida, digite um nome de exibição para o aplicativo. Por exemplo, **digite java-webapp**.
1. Selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** e selecione **o aplicativo Da Web / API** para **tipo de aplicativo**.
1. Selecione **Registrar** para registrar o aplicativo.
1. No menu à esquerda, selecione **Autenticação**. Em **Uris redirecionamento,** selecione **Web**.

1. Digite dois URIs de redirecionamento: um para a página de login e outro para a página de gráficos. Para ambos, use o mesmo número de host e porta, seguido por **/msal4jsample/secure/aad** para a página de login e **msal4jsample/graph/me** para a página de informações do usuário.

   Por padrão, a amostra usa:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Em seguida, **selecione Salvar**.

1. Selecione **Certificados & segredos** do menu.
1. Na seção Segredos do **Cliente,** selecione **Novo segredo do cliente**e, em seguida:

   1. Digite uma descrição chave.
   1. Selecione a duração da chave **Em 1 ano**.
   1. Selecione **Adicionar**.
   1. Quando o valor-chave aparecer, copie-o para depois. Este valor não será exibido novamente ou recuperável por qualquer outro meio.

# <a name="python"></a>[Python](#tab/python)

1. Quando a **página registrar uma página de aplicativo** for exibida, digite as informações de registro do seu aplicativo:
   1. Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo. Por exemplo, digite **python-webapp**.
   1. Alterar **os tipos de conta suportadas** para contas em qualquer diretório organizacional e contas pessoais da Microsoft **(por exemplo, Skype, Xbox, Outlook.com)**.
   1. Na seção **Redirecionar URI (opcional),** selecione **Web** na caixa de **http://localhost:5000/getAToken**combinação e digite o seguinte uri de redirecionamento: .
1. Selecione **Registrar** para criar o aplicativo.
1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior. Você precisará dele para definir o arquivo de configuração do Visual Studio para este projeto.
1. No menu à esquerda, **selecione Certificados & segredos**.
1. Na seção Segredos do **Cliente,** selecione **Novo segredo de cliente**e, em seguida:

   1. Digite uma descrição chave.
   1. Selecione uma duração de chave igual a **Em 1 ano**.
   1. Selecione **Adicionar**.
   1. Quando o valor-chave aparecer, copie-o. Você precisará dela mais tarde.
---

## <a name="register-an-app-by-using-powershell"></a>Registre um aplicativo usando o PowerShell

> [!NOTE]
> Atualmente, o Azure AD PowerShell cria aplicativos com apenas os seguintes tipos de conta suportados:
>
> - MyOrg (contas somente neste diretório organizacional)
> - AnyOrg (contas em qualquer diretório organizacional)
>
> Você pode criar um aplicativo que insere usuários com suas contas pessoais da Microsoft (por exemplo, Skype, Xbox ou Outlook.com). Primeiro, crie um aplicativo multilocatário. Os tipos de conta suportados são contas em qualquer diretório organizacional. Em seguida, `signInAudience` altere a propriedade no manifesto de aplicação do portal Azure. Para obter mais informações, consulte o [passo 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) no tutorial do ASP.NET Core. Você pode generalizar este passo para aplicativos web em qualquer idioma.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-web-app-sign-user-app-configuration.md)
