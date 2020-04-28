---
title: Compilar um aplicativo Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo Web que chama APIs da Web (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5af9e34baf6115e801fbfe35e6e3895e48b360e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80881716"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Cenário: um aplicativo Web que chama APIs da Web

Saiba como criar um aplicativo Web que conecta usuários à plataforma de identidade da Microsoft e, em seguida, chama APIs da Web em nome do usuário conectado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Este cenário pressupõe que você já passou pelo seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicativo Web que conecta os usuários](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Visão geral

Você adiciona autenticação ao seu aplicativo Web para que ele possa conectar usuários e chamar uma API da Web em nome do usuário conectado.

![Aplicativo Web que faz chamadas a APIs Web](./media/scenario-webapp/web-app.svg)

Os aplicativos Web que chamam APIs da Web são aplicativos cliente confidenciais.
É por isso que eles registram um segredo (uma senha de aplicativo ou certificado) com Azure Active Directory (Azure AD). Esse segredo é passado durante a chamada para o Azure AD para obter um token.

## <a name="specifics"></a>Especificações

> [!NOTE]
> A adição de entrada a um aplicativo Web é sobre a proteção do próprio aplicativo Web. Essa proteção é obtida usando bibliotecas de *middleware* , não a MSAL (biblioteca de autenticação da Microsoft). O cenário anterior, o [aplicativo Web que conecta os usuários](scenario-web-app-sign-user-overview.md), abordou esse assunto.
>
> Este cenário aborda como chamar APIs Web de um aplicativo Web. Você deve obter tokens de acesso para essas APIs da Web. Você usa bibliotecas MSAL para adquirir esses tokens.

O desenvolvimento para esse cenário envolve estas tarefas específicas:

- Durante o [registro do aplicativo](scenario-web-app-call-api-app-registration.md), você deve fornecer um URI de resposta, um segredo ou um certificado a ser compartilhado com o Azure AD. Se você implantar seu aplicativo em vários locais, você fornecerá essas informações para cada local.
- A [configuração do aplicativo](scenario-web-app-call-api-app-configuration.md) deve fornecer as credenciais do cliente que foram compartilhadas com o Azure ad durante o registro do aplicativo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Um aplicativo Web que chama APIs da Web: registro de aplicativo](scenario-web-app-call-api-app-registration.md)
