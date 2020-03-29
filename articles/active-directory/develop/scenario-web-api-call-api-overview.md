---
title: Construa uma API web que chame APIs web - plataforma de identidade Microsoft | Azure
description: Aprenda a construir uma API web que chama APIs web downstream (visão geral).
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701732"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Cenário: Uma API web que chama APIs da Web

Saiba o que você precisa saber para construir uma API web que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

Este cenário, no qual uma API web protegida chama APIs da Web, se baseia no cenário "Proteja uma API web". Para saber mais sobre esse cenário fundamental, consulte [Cenário: API web protegida](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Visão geral

- Um cliente de aplicativo web, desktop, móvel ou de uma página única (não representado no diagrama que acompanha) chama uma API web protegida e fornece um token json web token (JWT) em seu cabeçalho HTTP "Autorização".
- A API da Web protegida valida o token e `AcquireTokenOnBehalfOf` usa o método Microsoft Authentication Library (MSAL) para solicitar outro token do Azure Active Directory (Azure AD) para que a API da Web protegida possa chamar uma segunda API web, ou API web downstream, em nome do usuário.
- A API da Web `AcquireTokenSilent`protegida também pode chamar mais tarde para solicitar tokens para outras APIs downstream em nome do mesmo usuário. `AcquireTokenSilent`atualiza o token quando necessário.

![Diagrama de uma API web chamando uma API web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Especificidades

A parte de registro do aplicativo relacionada às permissões de API é clássica. A configuração do aplicativo envolve o uso do fluxo OAuth 2.0 on-behalf-Of para trocar o token do portador JWT por um token por uma API a jusante. Esse token é adicionado ao cache de tokens, onde está disponível nos controladores da API da Web, e pode então adquirir um token silenciosamente para chamar APIs a jusante.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do aplicativo](scenario-web-api-call-api-app-registration.md)
