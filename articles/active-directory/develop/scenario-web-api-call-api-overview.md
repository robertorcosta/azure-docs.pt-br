---
title: Criar uma API Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar uma API Web que chama APIs da Web downstream (visão geral).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4dcf377797709b56b4db735dabf4d48cfae4fc06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257156"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Cenário: uma API Web que chama APIs da Web

Saiba o que você precisa saber para criar uma API Web que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

Esse cenário, no qual uma API Web protegida chama outras APIs da Web, baseia-se no [cenário: API Web protegida](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Visão geral

- Um cliente de aplicativo Web, de área de trabalho, móvel ou de página única (não representado no diagrama que o acompanha) chama uma API Web protegida e fornece um token de portador JWT (token Web JSON) em seu cabeçalho HTTP "Authorization".
- A API Web protegida valida o token e usa o método MSAL (biblioteca de autenticação da Microsoft) `AcquireTokenOnBehalfOf` para solicitar outro token de Azure Active Directory (AD do Azure) para que a API Web protegida possa chamar uma segunda API da Web ou uma API da Web downstream, em nome do usuário.
- A API Web protegida também pode chamar `AcquireTokenSilent` mais tarde para solicitar tokens para outras APIs de downstream em nome do mesmo usuário. `AcquireTokenSilent` atualiza o token quando necessário.

![Diagrama de uma API Web chamando uma API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Especificações

A parte de registro do aplicativo relacionada às permissões de API é clássica. A configuração do aplicativo envolve o uso do fluxo em nome de do OAuth 2,0 para trocar o token de portador JWT em relação a um token para uma API downstream. Esse token é adicionado ao cache de token, onde está disponível nos controladores da API Web, e ele pode adquirir um token silenciosamente para chamar as APIs de downstream.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do aplicativo](scenario-web-api-call-api-app-registration.md)
