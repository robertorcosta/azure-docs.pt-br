---
title: Suporte a AD FS (MSAL para Java)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte ao Active Directory Federation Services (AD FS) na Microsoft Authentication Library for Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696207"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Suporte aos Serviços da Federação de Diretórios Ativos no MSAL para Java

O Active Directory Federation Services (AD FS) no Windows Server permite adicionar o OpenID Connect e a autenticação baseada em OAuth 2.0 e a autorização ao aplicativo Microsoft Authentication Library for Java (MSAL for Java). Uma vez integrado, seu aplicativo pode autenticar usuários em AD FS, federados através do Azure AD. Para obter mais informações sobre cenários, consulte [AD FS Scenarios for Developers](/windows-server/identity/ad-fs/ad-fs-development).

Um aplicativo que usa O MSAL para Java falará com o Azure Active Directory (Azure AD), que então federa o AD FS.

O MSAL for Java se conecta ao Azure AD, que sinaliza em usuários que são gerenciados no Azure AD (usuários gerenciados) ou usuários gerenciados por outro provedor de identidade, como o AD FS (usuários federados). MSAL para Java não sabe que um usuário é federado. Ele simplesmente fala com a Azure AD.

A [autoridade](msal-client-application-configuration.md#authority) que você usa neste caso é a autoridade usual (nome de hospedagem de autoridade + inquilino, comum ou organizações).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Adquira um token interativamente para um usuário federado

Quando você `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` liga `AuthorizationCodeParameters` `DeviceCodeParameters`ou com ou , a experiência do usuário é tipicamente:

1. O usuário insere seu ID de conta.
2. O Azure AD exibe brevemente "Levando você para a página da sua organização", e o usuário é redirecionado para a página de login do provedor de identidade. A página de login geralmente é personalizada com o logotipo da organização.

As versões AD FS suportadas neste cenário federado são:
- Serviços ativos da Federação de Diretórios FS v2
- Serviços de Federação de Diretórios Ativos v3 (Windows Server 2012 R2)
- Serviços de Federação de Diretórios Ativos v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Adquira um token via nome de usuário e senha

Quando você adquire um `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` token usando ou com `IntegratedWindowsAuthenticationParameters` ou , `UsernamePasswordParameters`MSAL para Java, o provedor de identidade entra em contato com base no nome de usuário. O MSAL for Java recebe um [token SAML 1.1](reference-saml-tokens.md) do provedor de identidade, que ele fornece ao Azure AD, que retorna o JSON Web Token (JWT).

## <a name="next-steps"></a>Próximas etapas

Para o caso federado, consulte [Configurar o login ativo do Azure no comportamento de um aplicativo usando uma diretiva Home Realm Discovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)