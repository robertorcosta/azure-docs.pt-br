---
title: Tipos de aplicativos para plataforma de identidade Microsoft | Azure
description: Os tipos de aplicativos e cenários suportados pela plataforma de identidade Microsoft (v2.0) endpoint.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: bdbda8bed38819ca2b4d2fb1ef3d9bf591269890
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535903"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Tipos de aplicativos para plataforma de identidade Microsoft

O ponto de extremidade da plataforma de identidade da Microsoft (v2.0) dá suporte à autenticação para diversas arquiteturas de aplicativos modernas, todas baseadas em protocolos padrão da indústria [OAuth 2.0 ou OpenID Connect](active-directory-v2-protocols.md). Este artigo descreve os tipos de aplicativos que você pode construir usando a plataforma de identidade Microsoft, independentemente do seu idioma ou plataforma preferido. As informações foram projetadas para ajudá-lo a entender cenários de alto nível antes de [começar a trabalhar com o código](v2-overview.md#getting-started).

> [!NOTE]
> O ponto final da plataforma de identidade da Microsoft não suporta todos os cenários e recursos do Azure Active Directory (Azure AD). Para determinar se você deve usar o ponto final da plataforma de identidade da Microsoft, leia sobre [as limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Você deve registrar cada aplicativo que usa o ponto final da plataforma de identidade da Microsoft no novo [portal de registros do App](https://go.microsoft.com/fwlink/?linkid=2083908). O processo de registro do aplicativo coleta e atribui estes valores para seu aplicativo:

* Um **ID de aplicativo (cliente)** que identifica exclusivamente seu aplicativo
* Um **URI de Redirecionamento** que pode ser usado para direcionar as respostas novamente ao aplicativo
* Alguns outros valores específicos do cenário, como tipos de conta suportados

Para obter detalhes, saiba como [registrar um aplicativo](quickstart-register-app.md).

Depois que o aplicativo é registrado, o aplicativo se comunica com a plataforma de identidade da Microsoft enviando solicitações para o ponto final. Fornecemos as estruturas e as bibliotecas de software livre que lidam com os detalhes dessas solicitações. Você também tem a opção de implementar a lógica de autenticação ao criar solicitações para esses pontos de extremidade:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplicativos de página única (JavaScript)

Muitos aplicativos modernos têm um aplicativo de página única front-end que é escrito principalmente em JavaScript. Muitas vezes, é escrito usando uma estrutura como Angular, React ou Vue. O ponto final da plataforma de identidade da Microsoft suporta esses aplicativos usando o [fluxo implícito OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

Nesse fluxo, o aplicativo recebe tokens diretamente da plataforma de identidade da Microsoft que autorizam o endpoint, sem quaisquer trocas de servidor para servidor. Todo o manuseio de lógica de autenticação e de sessão ocorra inteiramente no cliente JavaScript, sem redirecionamentos adicionais de página.

![Mostra o fluxo de autenticação implícito](./media/v2-app-types/convergence-scenarios-implicit.svg)

Para ver esse cenário em ação, experimente uma das amostras de código de aplicativo de uma página na [plataforma de identidade da Microsoft para começar a](v2-overview.md#getting-started) seção.

## <a name="web-apps"></a>Aplicativos Web

Para os aplicativos Web (.NET, PHP, Java, Ruby, Python, Node) que o usuário acessa por meio de um navegador, você pode usar o [OpenID Connect](active-directory-v2-protocols.md) para entrada do usuário. No OpenID Connect, o aplicativo Web recebe um token de ID. Um token de ID é um token de segurança que verifica a identidade do usuário e fornece informações sobre o usuário na forma de declarações:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Mais detalhes sobre diferentes tipos de tokens usados no ponto final da plataforma de identidade da Microsoft estão disponíveis na referência [de token de acesso](access-tokens.md) e id_token [referência](id-tokens.md)

Em aplicativos de servidor Web, o fluxo de autenticação de entrada usa estas etapas de alto nível:

![Mostra o fluxo de autenticação do aplicativo web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Você pode garantir a identidade do usuário validando o token de Identificação com uma chave de assinatura pública que é recebida do ponto final da plataforma de identidade da Microsoft. Um cookie de sessão é definido e pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, experimente uma das amostras de código de login do aplicativo web na [plataforma de identidade da Microsoft, iniciando a](v2-overview.md#getting-started) seção.

Além de entrada simples, um aplicativo de servidor Web talvez precise acessar outro serviço Web como uma API REST. Nesse caso, o aplicativo de servidor Web participa de um fluxo do OpenID Connect e do OAuth 2.0 combinados, usando o [fluxo do código de autorização do OAuth 2.0](active-directory-v2-protocols.md). Para obter mais informações sobre esse cenário, leia sobre [como começar com aplicativos web e APIs web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>APIs da Web

Você pode usar o ponto final da plataforma de identidade da Microsoft para proteger serviços web, como a API web RESTful do seu aplicativo. As APIs da Web podem ser implementadas em inúmeras plataformas e idiomas. Eles também podem ser implementados usando gatilhos HTTP em funções do Azure. Em vez de tokens de ID e cookies de sessão, uma API da Web usa um token de acesso OAuth 2.0 para proteger seus dados e autenticar solicitações recebidas. O chamador de uma API web anexa um token de acesso no cabeçalho de autorização de uma solicitação HTTP, como esta:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API da Web usa o token de acesso para verificar a identidade do chamador da API e extrair informações sobre o chamador de alegações codificadas no token de acesso. Mais detalhes sobre diferentes tipos de tokens usados no ponto final da plataforma de identidade da Microsoft estão disponíveis na referência [de token de acesso](access-tokens.md) e [id_token](id-tokens.md) referência.

Uma API web pode dar aos usuários o poder de optar ou desativar funcionalidades ou dados específicos, expondo permissões, também conhecidas como [escopos](v2-permissions-and-consent.md). Para um aplicativo de chamada obter permissão para um escopo, o usuário deve concordar com o escopo durante um fluxo. O ponto final da plataforma de identidade da Microsoft pede permissão ao usuário e, em seguida, registra permissões em todos os tokens de acesso que a API da Web recebe. A API web valida os tokens de acesso que recebe em cada chamada e realiza verificações de autorização.

Uma API web pode receber tokens de acesso de todos os tipos de aplicativos, incluindo aplicativos de servidor web, aplicativos de desktop e dispositivos móveis, aplicativos de uma página única, daemons do lado do servidor e até mesmo outras APIs da Web. O fluxo de alto nível para uma API web é assim:

![Mostra o fluxo de autenticação da API web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Para saber como proteger uma API web usando tokens de acesso OAuth2, confira as amostras de código de API da Web na [seção de inicialismo](v2-overview.md#getting-started) da plataforma de identidade Da Microsoft.

Em muitos casos, as APIs da Web também precisam fazer solicitações de saída para outras APIs web downstream protegidas pela plataforma de identidade Microsoft. Para isso, as APIs da Web podem aproveitar o fluxo **on-behalf-Of,** que permite que a API web troque um token de acesso de entrada por outro token de acesso a ser usado em solicitações de saída. Para obter mais informações, consulte [a plataforma de identidade da Microsoft e o fluxo On-Behalf-Of do OAuth 2.0](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplicativos móveis e nativos

Aplicativos instalados por dispositivos, como aplicativos móveis e desktop, muitas vezes precisam acessar serviços de back-end ou APIs web que armazenam dados e executam funções em nome de um usuário. Esses aplicativos podem adicionar credenciais e autorização a serviços de back-end usando o [fluxo de código de autorização do OAuth 2.0](v2-oauth2-auth-code-flow.md).

Nesse fluxo, o aplicativo recebe um código de autorização do ponto final da plataforma de identidade da Microsoft quando o usuário faz o sinal. O código de autorização representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário conectado. O aplicativo pode trocar o código de autorização em segundo plano por um token de acesso e um token de atualização do OAuth 2.0. O aplicativo pode usar o token de acesso para autenticar apis da Web em solicitações HTTP e usar o token de atualização para obter novos tokens de acesso quando os tokens de acesso mais antigos expirarem.

![Mostra o fluxo de autenticação de aplicativos nativos](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemons e aplicativos do lado do servidor

Aplicativos que têm processos de longa duração ou que operam sem interação com um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web. Esses aplicativos podem se autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade delegada de um usuário) com o fluxo de credenciais do cliente OAuth 2.0. Você pode provar a identidade do aplicativo usando um certificado ou o segredo do cliente. Para obter mais informações, consulte [o aplicativo de console .NET Core usando a plataforma de identidade Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

Nesse fluxo, o aplicativo interage `/token` diretamente com o ponto final para obter acesso:

![Mostra o fluxo de autenticação do aplicativo daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Para construir um aplicativo daemon, consulte a [ documentação de credenciais do cliente ](v2-oauth2-client-creds-grant-flow.md) ou tente um [ aplicativo de amostra .NET ](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
