---
title: Mover o aplicativo Web que conecta os usuários à produção – plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo Web que faz logon de usuários (mover para produção)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a45b52ac10a44b6efd54c41b3fec1e61a47a35
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82181623"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Aplicativo Web que conecta usuários: mover para produção

Agora que você sabe como obter um token para chamar APIs da Web, saiba como movê-la para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Próximas etapas

### <a name="troubleshooting"></a>Solução de problemas

> [!NOTE]
> Quando os usuários entram no aplicativo Web pela primeira vez, eles precisarão consentir. No entanto, em algumas organizações, os usuários podem ver uma mensagem semelhante à seguinte:
>
> *AppName precisa de permissões para acessar recursos em sua organização que somente um administrador pode conceder. Peça a um administrador para conceder permissão a este aplicativo antes de poder usá-lo.*
>
> Isso ocorre porque o administrador de locatários **desabilitou** a capacidade de consentimento dos usuários. Nesse caso, você precisa entrar em contato com seus administradores de locatários para que eles façam um consentimento de administrador para os escopos exigidos pelo aplicativo.

### <a name="same-site"></a>Mesmo site

Verifique se você entendeu possíveis problemas com as novas versões do navegador Chrome

> [!div class="nextstepaction"]
> [Como lidar com alterações de cookie SameSite no navegador Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

O pacote NuGet Microsoft. Identity. Web lida com os problemas mais comuns de SameSite.

### <a name="scenario-for-calling-web-apis"></a>Cenário para chamar APIs Web

Depois que seu aplicativo Web entra em usuários, ele pode chamar APIs da Web em nome dos usuários conectados. Chamar APIs da Web do aplicativo Web é o objeto do seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicativo Web que faz chamadas a APIs Web](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Aprofunde-se: tutorial do aplicativo Web ASP.NET Core

Saiba mais sobre outras maneiras de conectar usuários com este ASP.NET Core tutorial: 

> [!div class="nextstepaction"]
> [Habilite seus aplicativos Web para conectar usuários e chamar APIs com a plataforma de identidade da Microsoft para desenvolvedores](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Este tutorial progressivo tem código pronto para produção para um aplicativo Web, incluindo como adicionar entrada com contas no:

- Sua organização
- Várias organizações
- Contas corporativas ou de estudante ou contas pessoais da Microsoft
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nuvens nacionais

## <a name="sample-code-java-web-app"></a>Código de exemplo: aplicativo Web Java

Saiba mais sobre o aplicativo Web Java deste exemplo no GitHub: 

> [!div class="nextstepaction"]
> [Um aplicativo Web Java que conecta usuários com a plataforma de identidade da Microsoft e chama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
