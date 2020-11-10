---
title: Cenário de aplicativo de página única JavaScript – plataforma Microsoft Identity | Azure
description: Saiba como criar um aplicativo de página única (visão geral do cenário) usando a plataforma de identidade da Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 17acb2bc5e96a136f31371c0be912c2c758c0f76
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443850"
---
# <a name="scenario-single-page-application"></a>Cenário: Aplicativo de página única

Saiba tudo o que você precisa para criar um aplicativo de página única (SPA).

## <a name="getting-started"></a>Introdução

Se você ainda não fez isso, crie seu primeiro aplicativo concluindo o início rápido do JavaScript SPA:

[Início rápido: aplicativo de página única](./quickstart-v2-javascript.md)

## <a name="overview"></a>Visão geral

Muitos aplicativos Web modernos são criados como aplicativos de página única do lado do cliente. Os desenvolvedores os escrevem usando JavaScript ou uma estrutura SPA, como Angular, Vue e React. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos Web do lado do servidor tradicionais.

A plataforma Microsoft Identity fornece **duas** opções para habilitar aplicativos de página única para conectar usuários e obter tokens para acessar serviços de back-end ou APIs da Web:

- [Fluxo do código de autorização do OAuth 2.0 (com PKCE)](./v2-oauth2-auth-code-flow.md). O fluxo de código de autorização permite que o aplicativo troque um código de autorização para tokens de **ID** representarem o usuário autenticado e tokens de **Acesso** necessários para chamar APIs protegidas. Além disso, ele retorna tokens de **Atualização** que fornecem acesso de longo prazo a recursos em nome de usuários sem exigir interação com esses usuários. Essa é a abordagem **recomendada**.

![Aplicativos de página única – autenticação](./media/scenarios/spa-app-auth.svg)

- [Fluxo implícito do OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). O fluxo de concessão implícita permite que o aplicativo obtenha os tokens de **ID** e **Acesso**. Diferentemente do fluxo de código de autorização, o fluxo de concessão implícita não retorna um **token de Atualização**.

![Aplicativo de página única – implícito](./media/scenarios/spa-app.svg)

Esse fluxo de autenticação não inclui cenários de aplicativos que usam estruturas JavaScript de multiplataforma, como o Electron e React-Native. Eles exigem mais recursos para interação com as plataformas nativas.

## <a name="specifics"></a>Especificações

Para habilitar esse cenário para seu aplicativo, você precisa de:

* Registro de aplicativo com Azure Active Directory (Azure AD). As etapas de registro são diferentes entre o fluxo de concessão implícito e o fluxo de código de autorização.
* Configuração de aplicativo com as propriedades do aplicativo registrado, como a ID do aplicativo.
* Usar a biblioteca de autenticação da Microsoft para JavaScript (MSAL.js) para fazer o fluxo de autenticação para entrar e adquirir tokens.

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, registro de [aplicativo](scenario-spa-app-registration.md).
