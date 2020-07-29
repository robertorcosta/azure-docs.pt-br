---
title: Mover o aplicativo de página única para produção-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de página única (mover para produção)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 777f3de8f2872e378fe30cc50ee0a5eb3823a625
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82900321"
---
# <a name="single-page-application-move-to-production"></a>Aplicativo de página única: mover para produção

Agora que você sabe como adquirir um token para chamar APIs da Web, saiba como mover para produção.

## <a name="improve-your-app"></a>Aprimore seu aplicativo

[Habilite o registro em log](msal-logging.md) para tornar a produção do aplicativo pronta.

## <a name="test-your-integration"></a>Testar sua integração

Teste sua integração seguindo a [lista de verificação de integração da plataforma de identidade da Microsoft](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Próximas etapas

Aprofunde-se no exemplo de início rápido, que explica o código de como conectar usuários e obter um token de acesso para chamar a API de Microsoft Graph usando MSAL.js:

> [!div class="nextstepaction"]
> [Tutorial do SPA do JavaScript](./tutorial-v2-javascript-spa.md)

Exemplo que demonstra como obter tokens para sua própria API Web de back-end usando MSAL.js:

> [!div class="nextstepaction"]
> [SPA com um back-end ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

Exemplo que mostra como usar MSAL.js para conectar usuários em um aplicativo que está registrado com Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA com Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
