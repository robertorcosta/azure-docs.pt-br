---
title: Crie um aplicativo móvel que chame APIs web | Azure
titleSuffix: Microsoft identity platform | Azure
description: Saiba como criar um aplicativo móvel que chama APIs da Web (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1f90f7f23fbdf10b91d8dfc7cd00cca83cd32fbc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882566"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Cenário: Aplicativo móvel que chama APIs web

Aprenda a criar um aplicativo móvel que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Crie seu primeiro aplicativo móvel e experimente um início rápido.

> [!div class="nextstepaction"]
> [Quickstart: adquira um token e ligue para a Microsoft Graph API a partir de um aplicativo para Android](./quickstart-v2-android.md)
>
> [Quickstart: adquira um token e chame a API do Microsoft Graph a partir de um aplicativo para iOS](./quickstart-v2-ios.md)
>
> [Quickstart: Adquira um token e chame a API do Microsoft Graph a partir de um aplicativo Xamarin para iOS e Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Visão geral

Uma experiência personalizada e perfeita do usuário é essencial para aplicativos móveis.  A plataforma de identidade da Microsoft permite que desenvolvedores móveis criem essa experiência para usuários de iOS e Android. Seu aplicativo pode fazer login nos usuários do Azure Active Directory (Azure AD), usuários pessoais da conta microsoft e usuários do Azure AD B2C. Ele também pode adquirir tokens para chamar uma API web em seu nome. Para implementar esses fluxos, usaremos a Microsoft Authentication Library (MSAL). A MSAL implementa o fluxo de [código de autorização Padrão oAuth2.0 da](v2-oauth2-auth-code-flow.md)indústria .

![Aplicativos Daemon](./media/scenarios/mobile-app.svg)

Considerações para aplicativos móveis:

- **A experiência do usuário é fundamental**: Permita que os usuários vejam o valor do seu aplicativo antes de solicitar o login. Solicite apenas as permissões necessárias.
- **Suporte a todas as configurações do usuário**: Muitos usuários de negócios móveis devem aderir a políticas de acesso condicional e políticas de conformidade com dispositivos. Certifique-se de apoiar esses cenários-chave.
- **Implementar o single sign-on (SSO)**: Usando a plataforma de identidade MSAL e Microsoft, você pode habilitar o login único através do navegador do dispositivo ou do Microsoft Authenticator (e do Portal da Empresa Intune no Android).
- **Implementar modo de dispositivo compartilhado**: Permita que seu aplicativo seja usado em cenários de dispositivos compartilhados, por exemplo, hospitais, manufatura, varejo e finanças. [Leia mais sobre o suporte ao modo dispositivo compartilhado](msal-shared-devices.md).

## <a name="specifics"></a>Especificidades

Tenha em mente as seguintes considerações ao criar um aplicativo móvel na plataforma de identidade da Microsoft:

- Dependendo da plataforma, alguma interação do usuário pode ser necessária na primeira vez que os usuários entrarem. Por exemplo, o iOS requer que os aplicativos mostrem a interação do usuário quando eles usam o SSO pela primeira vez através do Microsoft Authenticator (e do Portal intune Company no Android).
- No iOS e Android, o MSAL pode usar um navegador externo para fazer login nos usuários. O navegador externo pode aparecer no topo do seu aplicativo.
- Nunca use um segredo em um aplicativo móvel. Nesses aplicativos, os segredos são acessíveis a todos os usuários.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do aplicativo](scenario-mobile-app-registration.md)
