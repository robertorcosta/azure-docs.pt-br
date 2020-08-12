---
title: Configurar o logon único do aplicativo
description: Como configurar o logon único para um aplicativo personalizado que você está desenvolvendo e registrar com o Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 465adbb71abaa45160399ecba2ebfb692a8307c2
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120670"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Como configurar o logon único para um aplicativo

Habilitar logon único federado (SSO) em seu aplicativo é habilitado automaticamente por meio do Azure AD para OpenID Connect, SAML 2.0 ou WS-Fed. Se os usuários finais estão precisando entrar, mesmo já tendo uma sessão existente com o Azure AD, é provável que seu aplicativo possa estar configurado incorretamente.

* Se você estiver usando o ADAL/MSAL, verifique se você tem o **PromptBehavior** definido como **Automático** em vez de **Sempre**.

* Se você estiver criando um aplicativo móvel, talvez seja necessário configurações adicionais para habilitar SSO orientado ou não orientado.

Para o Android, consulte [Habilitando SSO entre aplicativos no Android](../azuread-dev/howto-v1-enable-sso-android.md).<br>

Para o iOS, consulte [Habilitando SSO entre aplicativos no iOS](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Próximas etapas

[SSO do Azure AD](../manage-apps/what-is-single-sign-on.md)<br>

[Habilitando SSO entre aplicativos no Android](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Habilitando SSO entre aplicativos no iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Integrando aplicativos ao AzureAD](./quickstart-register-app.md)<br>

[Permissões e consentimento no ponto de extremidade da plataforma de identidade da Microsoft](./v2-permissions-and-consent.md)<br>

[StackOverflow do AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)