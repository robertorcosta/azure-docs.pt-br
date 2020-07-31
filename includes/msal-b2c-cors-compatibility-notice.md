---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311527"
---
> [!IMPORTANT]
> Atualmente o MSAL.js 2.0 não é compatível com o Azure AD B2C para uso com o fluxo de código de autorização PKCE. Neste momento, o Azure AD B2C recomenda o uso do fluxo implícito, conforme descrito no [Tutorial: registrar um aplicativo][implicit-flow]. Para seguir o progresso desse problema, consulte o [wiki do MSAL.js][msal-wiki].

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue
