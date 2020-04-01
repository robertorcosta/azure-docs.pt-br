---
title: Amostras de código para plataforma de identidade Microsoft | Microsoft Docs
description: Fornece um índice de amostras de código disponíveis da plataforma de identidade Microsoft (ponto final v2.0), organizada por cenário.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d8f69096544b0c3b51513d0cb5c4330fcbb1c18c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419824"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Amostras de código de plataforma de identidade da Microsoft (ponto final v2.0)

Você pode usar a plataforma de identidade da Microsoft para:

- Adicionar autenticação e autorização para aplicativos Web e APIs da Web.
- Solicitar um token de acesso para acessar uma API da Web protegida.

Este artigo descreve brevemente e fornece links para amostras para o ponto final da plataforma de identidade Microsoft. Essas amostras mostram como é feito, e também fornecem trechos de código que você pode usar em seus aplicativos. Na página de exemplo de código, você encontrará tópicos detalhados de readme que ajudam com requisitos, instalação e configuração. Comentários dentro do código ajudam você a entender as seções críticas.

> [!NOTE]
> Se estiver interessado em amostras v1.0, consulte [amostras de código Azure AD (ponto final v1.0)](../azuread-dev/sample-v1-code.md).

Para entender o cenário básico de cada tipo de amostra, consulte [tipos de aplicativos para o ponto final da plataforma de identidade Microsoft](v2-app-types.md).

Você também pode contribuir com os exemplos no GitHub. Para saber como, consulte [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicativos de página única

Essas amostras mostram como escrever um aplicativo de uma página única protegido com a plataforma de identidade Microsoft. Estas amostras usam um dos sabores de MSAL.js.

| Plataforma | Descrição | Link |
| -------- | --------------------- | -------- |
| ![Esta imagem mostra o](media/sample-v2-code/logo_js.png) logotipo [JavaScript JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chama o Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Esta imagem mostra o](media/sample-v2-code/logo_js.png) logotipo [JavaScript JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chama B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Esta imagem mostra o](media/sample-v2-code/logo_js.png) logotipo [JavaScript JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Chama a própria API web |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Esta imagem mostra](media/sample-v2-code/logo_angular.png) o logotipo angular [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Chama o Microsoft Graph  | [ativo-diretório-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Esta imagem mostra](media/sample-v2-code/logo_angular.png) o logotipo angular [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Chama B2C |[ativo-diretório-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Aplicativos Web

Os exemplos a seguir ilustram os aplicativos Web que iniciam sessão de usuários. Alguns exemplos também demonstram o aplicativo chamando o Microsoft Graph ou sua própria API Web com a identidade do usuário.

| Plataforma | Conecta apenas usuários | Conecta usuários e chama o Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Esta imagem mostra o logotipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>núcleo ASP.NET 2.2 | [ASP.NET tutorial de entrada de usuários do WebApp do Core](https://aka.ms/aspnetcore-webapp-sign-in) | Mesma amostra no [ASP.NET Web App chama fase do Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![Esta imagem mostra o logotipo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Início rápido do ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Esta imagem mostra o logotipo Java](media/sample-v2-code/logo_java.png)  |                   | [ms-identidade-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Esta imagem mostra o logotipo python](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Esta imagem mostra o logotipo ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicativos cliente públicos de desktop e móveis

As amostras a seguir mostram aplicativos clientes públicos (aplicativos desktop ou mobile) que acessam a API do Microsoft Graph ou sua própria API web em nome de um usuário. Todos esses aplicativos clientes usam a Microsoft Authentication Library (MSAL).

| Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph | Chama uma API web ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Desktop (WPF)      | ![Esta imagem mostra o logotipo .NET/C#](media/sample-v2-code/logo_NET.png) | [Interativo](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo .NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo Java](media/sample-v2-code/logo_java.png) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identidade-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Nome de usuário/senha](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Desktop (Console) com WAM  | ![Esta imagem mostra o logotipo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [interativo com WAM](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo Java](media/sample-v2-code/logo_java.png) | [Nome de usuário/senha](msal-authentication-flows.md#usernamepassword) |[ms-identidade-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo python](media/sample-v2-code/logo_python.png) | [Nome de usuário/senha](msal-authentication-flows.md#usernamepassword) |[ms-identidade-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Móveis (Android, iOS, UWP)   | ![Esta imagem mostra o logotipo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Interativo](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![Esta imagem mostra iOS/Objective-C ou Swift](media/sample-v2-code/logo_iOS.png) | [Interativo](msal-authentication-flows.md#interactive) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [Interativo](msal-authentication-flows.md#interactive) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Celular (Android-Java)   | ![Esta imagem mostra o logotipo do Android](media/sample-v2-code/logo_Android.png) | [Interativo](msal-authentication-flows.md#interactive) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Celular (Android-Kotlin)   | ![Esta imagem mostra o logotipo do Android](media/sample-v2-code/logo_Android.png) | [Interativo](msal-authentication-flows.md#interactive) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplicativos de daemon

Os exemplos a seguir mostram um aplicativo que acessa a API do Microsoft Graph com sua própria identidade (sem usuários).

| Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Esta imagem mostra o logotipo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenciais do cliente](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplicativo Web | ![Esta imagem mostra o logotipo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenciais do cliente](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![Esta imagem mostra o logotipo Java](media/sample-v2-code/logo_java.png) | [Credenciais do cliente](msal-authentication-flows.md#client-credentials) | [ms-identidade-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Esta imagem mostra o logotipo python](media/sample-v2-code/logo_python.png) | [Credenciais do cliente](msal-authentication-flows.md#client-credentials) | [ms-identidade-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Aplicativos sem periféricos

O exemplo a seguir mostra um aplicativo cliente público em execução em um dispositivo sem um navegador da Web. O aplicativo pode ser uma ferramenta de linha de comando, um aplicativo rodando no Linux ou Mac, ou um aplicativo IoT. A amostra apresenta um aplicativo acessando a API do Microsoft Graph, em nome de um usuário que faz logon interativamente em outro dispositivo (como um telefone celular). Este aplicativo cliente usa a Microsoft Authentication Library (MSAL).

| Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo Java](media/sample-v2-code/logo_java.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[ms-identidade-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo python](media/sample-v2-code/logo_python.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[ms-identidade-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>APIs da Web

As amostras a seguir mostram como proteger uma API web com o ponto final da plataforma de identidade Da Microsoft e como chamar uma API a jusante da API web.

| Plataforma | Amostra |
| -------- | ------------------- |
| ![Esta imagem mostra o logotipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>núcleo ASP.NET 2.2 | ASP.NET API web do [núcleo de dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Esta imagem mostra o logotipo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | API web (serviço) de [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Esta imagem mostra o logotipo Java](media/sample-v2-code/logo_java.png) | API web (serviço) de [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Esta imagem mostra o logotipo node.js](media/sample-v2-code/logo_nodejs.png) | API web (serviço) de [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Esta imagem mostra o logotipo node.js](media/sample-v2-code/logo_nodejs.png) | API web B2C (serviço) de [ativo-diretório-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Funções do Azure como APIs da Web

As amostras a seguir mostram como proteger uma função Azure usando httptrigger e expondo uma API web com o ponto final da plataforma de identidade Microsoft e como chamar uma API a jusante da API da Web.

| Plataforma | Amostra |
| -------- | ------------------- |
| ![Esta imagem mostra o logotipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>núcleo ASP.NET 2.2 | ASP.NET API web Core (serviço) Função Azure do [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Esta imagem mostra o logotipo node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | API web (serviço) de [NodeJS e passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Esta imagem mostra o logotipo python](media/sample-v2-code/logo_python.png)</p>Python | API web (serviço) de [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Esta imagem mostra o logotipo node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | API web (serviço) de [NodeJS e passport-azure-ad usando em nome de](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para saber mais sobre [exemplos](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstram os diferentes padrões de uso para a API do Microsoft Graph, incluindo autenticação com o Microsoft Azure AD, consulte [Exemplos e tutoriais da Comunidade do Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Confira também

- [Guia do desenvolvedor do Azure Active Directory (v1.0)](../azuread-dev/v1-overview.md)
- [API e referência do Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
