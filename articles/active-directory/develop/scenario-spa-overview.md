---
title: Cenário do aplicativo de página única JavaScript - Plataforma de identidade da Microsoft | Azure
description: Aprenda a construir um aplicativo de uma página única (visão geral do cenário) usando a plataforma de identidade microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701868"
---
# <a name="scenario-single-page-application"></a>Cenário: Aplicativo de página única

Aprenda tudo o que você precisa para construir um aplicativo de uma página única (SPA).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Você pode criar seu primeiro aplicativo seguindo o quickstart do JavaScript SPA:

> [!div class="nextstepaction"]
> [Quickstart: Aplicativo de página única](./quickstart-v2-javascript.md)

## <a name="overview"></a>Visão geral

Muitos aplicativos web modernos são construídos como aplicativos de página única do lado do cliente. Os desenvolvedores os escrevem usando JavaScript ou uma estrutura SPA como Angular, Vue.js e React.js. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos web tradicionais do lado do servidor. 

A plataforma de identidade da Microsoft permite que aplicativos de página única entrem nos usuários e obtenham tokens para acessar serviços back-end ou APIs da Web usando o [fluxo implícito OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). O fluxo implícito permite que o aplicativo obtenha tokens de ID para representar o usuário autenticado e também acessar tokens necessários para chamar APIs protegidas.

![Aplicativos de página única](./media/scenarios/spa-app.svg)

Esse fluxo de autenticação não inclui cenários de aplicativos que usam frameworks JavaScript multiplataforma, como Electron e React-Native. Eles requerem mais recursos para interação com as plataformas nativas.

## <a name="specifics"></a>Especificidades

Para habilitar este cenário para sua aplicação, você precisa:

* Registro de inscrição no Azure Active Directory (Azure AD). Esse registro envolve ativar o fluxo implícito e definir um URI de redirecionamento para o qual os tokens são devolvidos.
* Configuração do aplicativo com as propriedades do aplicativo cadastrado, como id de aplicativo.
* Usando a Microsoft Authentication Library (MSAL) para fazer o fluxo de autenticação para fazer login e adquirir tokens.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do aplicativo](scenario-spa-app-registration.md)
