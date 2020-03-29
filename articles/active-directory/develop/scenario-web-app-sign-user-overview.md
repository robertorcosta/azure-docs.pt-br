---
title: Faça login usuários a partir de um aplicativo web - plataforma de identidade da Microsoft | Azure
description: Saiba como construir um aplicativo web que faça sinais nos usuários (visão geral)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 403f589702fd7142f0515a3b6f19ee1b9bbb6420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701545"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Cenário: Web app que assina em usuários

Saiba tudo o que você precisa para construir um aplicativo web que usa a plataforma de identidade microsoft para fazer login nos usuários.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Se você quiser criar seu primeiro aplicativo web portátil (ASP.NET Core) que faz signatis nos usuários, siga este quickstart:

> [!div class="nextstepaction"]
> [Quickstart: ASP.NET aplicativo web Core que assina em usuários](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Se você quiser entender como adicionar login a um aplicativo web ASP.NET existente, tente o seguinte quickstart:

> [!div class="nextstepaction"]
> [Quickstart: ASP.NET web app que assina em usuários](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Se você é um desenvolvedor Java, tente o seguinte quickstart:

> [!div class="nextstepaction"]
> [Quickstart: Adicione login com a Microsoft a um aplicativo web Java](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Se você desenvolver com Python, tente o seguinte quickstart:

> [!div class="nextstepaction"]
> [Quickstart: adicione login com a Microsoft a um aplicativo web Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Visão geral

Você adiciona autenticação ao seu aplicativo web para que ele possa fazer login nos usuários. A adição de autenticação permite que seu aplicativo web acesse informações de perfil limitado, a fim de personalizar a experiência para os usuários. 

Os aplicativos da Web autenticam um usuário em um navegador da Web. Neste cenário, o aplicativo web direciona o navegador do usuário para inscrevê-los no Azure Active Directory (Azure AD). O Azure AD retorna uma resposta de login através do navegador do usuário, que contém reclamações sobre o usuário em um token de segurança. A assinatura de usuários aproveita o protocolo padrão [Open ID Connect,](./v2-protocols-oidc.md) simplificado pelo uso de [bibliotecas](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)de middleware .

![O aplicativo Web conecta usuários](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Como uma segunda fase, você pode habilitar seu aplicativo para chamar APIs da Web em nome do usuário inscrito. Esta próxima fase é um cenário diferente, que você encontrará no [aplicativo web que chama APIs web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Adicionar login a um aplicativo web é proteger o aplicativo web e validar um token de usuário, que é o que as bibliotecas **de middleware** fazem. No caso do .NET, esse cenário ainda não requer a Microsoft Authentication Library (MSAL), que trata de adquirir um token para chamar APIs protegidas. Bibliotecas de autenticação serão introduzidas no cenário de acompanhamento, quando o aplicativo web precisar chamar APIs da Web.

## <a name="specifics"></a>Especificidades

- Durante o registro do aplicativo, você precisará fornecer um ou vários (se você implantar seu aplicativo em vários locais) responder URIs. Em alguns casos (ASP.NET e ASP.NET Core), você precisará ativar o token de ID. Finalmente, você vai querer configurar um URI de assinatura para que seu aplicativo reaja aos usuários que se cadasitam.
- No código para o seu aplicativo, você precisará fornecer a autoridade para a qual seus delegados de aplicativos web se inscrevem. Você pode querer personalizar a validação de tokens (em particular, em cenários de parceiros).
- Os aplicativos da Web suportam qualquer tipo de conta. Para obter mais informações, consulte [tipos de conta suportados](v2-supported-account-types.md).

## <a name="next-steps"></a>Próximas etapas

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Registro do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Registro do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Registro do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Registro do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
