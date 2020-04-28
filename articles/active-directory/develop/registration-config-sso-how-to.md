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
ms.openlocfilehash: 630be1676d73410f1b0d376b163a8599299ddbe2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80883144"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Como configurar o logon único para um aplicativo

Habilitar logon único federado (SSO) em seu aplicativo é habilitado automaticamente por meio do Azure AD para OpenID Connect, SAML 2.0 ou WS-Fed. Se os usuários finais estão precisando entrar, mesmo já tendo uma sessão existente com o Azure AD, é provável que seu aplicativo possa estar configurado incorretamente.

* Se você estiver usando o ADAL/MSAL, verifique se você tem o **PromptBehavior** definido como **Automático** em vez de **Sempre**.

* Se você estiver criando um aplicativo móvel, talvez seja necessário configurações adicionais para habilitar SSO orientado ou não orientado.

Para o Android, consulte [Habilitando SSO entre aplicativos no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Para o iOS, consulte [Habilitando SSO entre aplicativos no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Próximas etapas

[SSO do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Habilitando SSO entre aplicativos no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Habilitando SSO entre aplicativos no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrando aplicativos ao AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Consentimento e permissão para aplicativos convergidos do AzureAD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow do AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)
