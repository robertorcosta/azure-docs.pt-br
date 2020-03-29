---
title: Mover web app que assina em usuários para produção - plataforma de identidade Microsoft | Azure
description: Saiba como construir um aplicativo web que faça sinais nos usuários (mude para produção)
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
ms.custom: aaddev
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768099"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Web app que assina nos usuários: Mova-se para a produção

Agora que você sabe como obter um token para chamar APIs da Web, aprenda como movê-lo para a produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Próximas etapas

### <a name="same-site"></a>Mesmo site

Certifique-se de entender possíveis problemas com novas versões do navegador Chrome

> [!div class="nextstepaction"]
> [Como lidar com as alterações de cookies do SameSite no navegador Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Cenário para chamar APIs da Web

Depois que seu aplicativo web assina nos usuários, ele pode chamar APIs da Web em nome dos usuários inscritos. Chamar APIs da Web a partir do aplicativo web é o objeto do seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicativo web que chama APIs da Web](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Mergulho profundo: tutorial do aplicativo web ASP.NET Core

Conheça outras maneiras de fazer login com usuários com este tutorial do ASP.NET Core: 

> [!div class="nextstepaction"]
> [Habilite que seus aplicativos web entrem nos usuários e chamem APIs com a plataforma de identidade da Microsoft para desenvolvedores](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Este tutorial progressivo tem código pronto para produção para um aplicativo web, incluindo como adicionar login com contas em:

- Sua organização
- Várias organizações
- Contas de trabalho ou escola, ou contas pessoais da Microsoft
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nuvens nacionais

## <a name="sample-code-java-web-app"></a>Código de exemplo: Java web app

Saiba mais sobre o aplicativo web Java a partir desta amostra no GitHub: 

> [!div class="nextstepaction"]
> [Um aplicativo Web Java que assina em usuários com a plataforma de identidade Microsoft e chama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
