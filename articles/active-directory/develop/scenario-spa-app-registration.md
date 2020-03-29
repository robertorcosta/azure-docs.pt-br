---
title: Registre aplicativos de página única - plataforma de identidade da Microsoft | Azure
description: Saiba como construir um aplicativo de uma página única (registro de aplicativos)
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
ms.custom: aaddev
ms.openlocfilehash: 5b18b10748e0587920c6965f1d235376da928469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701885"
---
# <a name="single-page-application-app-registration"></a>Aplicativo de página única: Registro de aplicativos

Esta página explica as especificidades de registro do aplicativo para um aplicativo de uma página única (SPA).

Siga as etapas para [registrar um novo aplicativo com a plataforma de identidade Microsoft](quickstart-register-app.md)e selecione as contas suportadas para o seu aplicativo. O cenário de SPA pode suportar autenticação com contas em sua organização ou qualquer organização e contas pessoais da Microsoft.

Em seguida, conheça os aspectos específicos do registro de inscrição que se aplicam a aplicativos de uma página.

## <a name="register-a-redirect-uri"></a>Registre um URI de redirecionamento

O fluxo implícito envia os tokens em um redirecionamento para o aplicativo de uma página única em execução em um navegador da Web. Por isso, é importante registrar um URI de redirecionamento onde seu aplicativo pode receber os tokens. Certifique-se de que o URI redirecionado corresponda exatamente ao URI para sua aplicação.

No [portal Azure,](https://go.microsoft.com/fwlink/?linkid=2083908)acesse seu aplicativo cadastrado. Na página **autenticação** do aplicativo, selecione a plataforma **Web.** Digite o valor do URI de redirecionamento para sua aplicação no campo **Redirecionar URI.**

## <a name="enable-the-implicit-flow"></a>Permitir o fluxo implícito

Na mesma página **autenticação,** em **Configurações Avançadas,** você também deve habilitar **a concessão implícita**. Se o aplicativo estiver apenas fazendo login nos usuários e recebendo tokens de ID, basta selecionar a caixa de seleção **de tokens de ID.**

Se o aplicativo também precisar obter tokens de acesso para chamar APIs, certifique-se de selecionar a caixa de seleção **de tokens** de acesso também. Para obter mais informações, consulte [tokens de ID](./id-tokens.md) e [tokens de acesso](./access-tokens.md).

## <a name="api-permissions"></a>Permissões de API

Os aplicativos de uma página única podem chamar APIs em nome do usuário inscrito. Eles precisam pedir permissões delegadas. Para obter detalhes, consulte [Adicionar permissões para acessar APIs da Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-spa-app-configuration.md)
