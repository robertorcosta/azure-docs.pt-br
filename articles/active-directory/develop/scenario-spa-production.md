---
title: Mover aplicativo de página única para produção
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 0c1e9e6da02478f5e4703676b74fc8247a4f619b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753517"
---
# <a name="single-page-application-move-to-production"></a>Aplicativo de página única: mover para produção

Agora que você sabe como adquirir um token para chamar APIs da Web, saiba como mover para produção.

## <a name="improve-your-app"></a>Aprimore seu aplicativo

[Habilite o registro em log](msal-logging.md) para tornar a produção do aplicativo pronta.

## <a name="test-your-integration"></a>Testar sua integração

Teste sua integração seguindo a [lista de verificação de integração da plataforma de identidade da Microsoft](identity-platform-integration-checklist.md).

## <a name="deploy-your-app"></a>Implante seu aplicativo

Confira um [exemplo de implantação](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) para aprender a implantar seus projetos de API da Web e do spa com o armazenamento do Azure e os serviços de Azure app, respectivamente. 

## <a name="next-steps"></a>Próximas etapas

- Aprofunde-se no exemplo de início rápido, que explica o código de como conectar usuários e obter um token de acesso para chamar a **API de Microsoft Graph** usando **MSAL.js**: tutorial de [Spa do JavaScript](./tutorial-v2-javascript-spa.md).

- Exemplo que demonstra como obter tokens para sua própria API Web de back-end (ASP.NET Core) usando **MSAL.js**: [spa com um back-end ASP.net](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi).

- Exemplo que demonstra como validar tokens de acesso para sua API Web de back-end (Node.js) usando **Passport-Azure-ad**: [ API Web doNode.js (AD do Azure](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)).

- Exemplo que mostra como usar **MSAL.js** para conectar usuários em um aplicativo que está registrado com **Azure Active Directory B2C** (Azure ad B2C): [spa com Azure ad B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

- Exemplo que mostra como usar o **Passport-Azure-ad** para validar tokens de acesso para aplicativos registrados com **Azure Active Directory B2C** (Azure ad B2C): [Node.js API da Web (Azure ad B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).
