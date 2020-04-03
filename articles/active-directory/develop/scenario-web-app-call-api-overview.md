---
title: Construa um aplicativo web que chame APIs web - plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo web que chama APIs da Web (visão geral)
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
ms.custom: aaddev
ms.openlocfilehash: 14f513306b3f0bc0c06a4143e5174c3ecddaef62
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617160"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Cenário: Um aplicativo web que chama APIs web

Saiba como criar um aplicativo web que inscreva usuários na plataforma de identidade da Microsoft e, em seguida, chama APIs da Web em nome do usuário inscrito.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Este cenário pressupõe que você já passou pelo seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicativo web que assina em usuários](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Visão geral

Você adiciona autenticação ao seu aplicativo web para que ele possa fazer login e chamar uma API web em nome do usuário inscrito.

![Aplicativo Web que chama as APIs Web](./media/scenario-webapp/web-app.svg)

Os aplicativos da Web que chamam APIs da Web são aplicativos clienteconfidenciais.
É por isso que eles registram um segredo (uma senha ou certificado de aplicativo) com o Azure Active Directory (Azure AD). Este segredo é passado durante a chamada para a Azure AD para obter um token.

## <a name="specifics"></a>Especificidades

> [!NOTE]
> Adicionar login a um aplicativo web é proteger o próprio aplicativo web. Essa proteção é alcançada usando bibliotecas *de middleware,* não a Microsoft Authentication Library (MSAL). O cenário anterior, [web app que assina em usuários,](scenario-web-app-sign-user-overview.md)cobriu esse assunto.
>
> Este cenário abrange como chamar APIs da Web a partir de um aplicativo web. Você deve obter tokens de acesso para essas APIs da Web. Você usa bibliotecas MSAL para adquirir esses tokens.

O desenvolvimento para este cenário envolve essas tarefas específicas:

- Durante [o registro da inscrição,](scenario-web-app-call-api-app-registration.md)você deve fornecer um URI de resposta, segredo ou certificado a ser compartilhado com o Azure AD. Se você implantar seu aplicativo em vários locais, você fornecerá essas informações para cada local.
- A [configuração](scenario-web-app-call-api-app-configuration.md) do aplicativo deve fornecer as credenciais do cliente que foram compartilhadas com o Azure AD durante o registro do aplicativo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Um aplicativo web que chama APIs web: registro de aplicativos](scenario-web-app-call-api-app-registration.md)
