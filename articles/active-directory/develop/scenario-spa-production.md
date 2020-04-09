---
title: Mover aplicativo de página única para produção - plataforma de identidade da Microsoft | Azure
description: Saiba como construir um aplicativo de uma página única (mude para produção)
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
ms.openlocfilehash: 04a900c98311f4e7dcccbfca93ea41e212308759
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882090"
---
# <a name="single-page-application-move-to-production"></a>Aplicativo de página única: Passar para a produção

Agora que você sabe como adquirir um token para chamar APIs da Web, aprenda a se mover para a produção.

## <a name="improve-your-app"></a>Melhore seu aplicativo

[Habilite](msal-logging.md) o registro para deixar a produção do aplicativo pronta.

## <a name="test-your-integration"></a>Teste sua integração

Teste sua integração seguindo a [lista de verificação de integração da plataforma de identidade da Microsoft](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Próximas etapas

Mergulho profundo da amostra quickstart, que explica o código de como fazer login nos usuários e obter um token de acesso para chamar a API do Microsoft Graph usando MSAL.js:

> [!div class="nextstepaction"]
> [Tutorial do JavaScript SPA](./tutorial-v2-javascript-spa.md)

Amostra que demonstra como obter tokens para sua própria API web back-end usando MSAL.js:

> [!div class="nextstepaction"]
> [SPA com um back-end ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Amostra que mostra como usar o MSAL.js para fazer login com usuários em um aplicativo registrado no Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA com Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
