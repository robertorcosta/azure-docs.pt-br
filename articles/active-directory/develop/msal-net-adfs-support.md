---
title: Suporte a AD FS em MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte ao Active Directory Federation Services (AD FS) na Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160752"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Suporte aos Serviços da Federação de Diretórios Ativos em MSAL.NET
O Active Directory Federation Services (AD FS) no Windows Server permite adicionar o OpenID Connect e a autenticação e a autorização baseadas no OAuth 2.0 aos aplicativos que você está desenvolvendo. Esses aplicativos podem, então, autenticar os usuários diretamente contra o AD FS. Para obter mais informações, leia [AD FS Scenarios for Developers](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

A Microsoft Authentication Library for .NET (MSAL.NET) suporta dois cenários para autenticação contra AD FS:

- MSAL.NET conversa com o Azure Active Directory, que em si é *federado* com AD FS.
- MSAL.NET fala **diretamente** com uma autoridade da ADFS. Isso só é suportado a partir do AD FS 2019 e acima. Um dos cenários que este destaca é o suporte [ao Azure Stack](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL conecta-se ao Azure AD, que é federado com AD FS
MSAL.NET suporta a conexão com o Azure AD, que sinaliza em usuários gerenciados (usuários gerenciados no Azure AD) ou usuários federados (usuários gerenciados por outro provedor de identidade, como o AD FS). MSAL.NET não sabe sobre o fato de que os usuários são federados. No que diz respeito a isso, ele fala com a Azure AD.

A [autoridade](msal-client-application-configuration.md#authority) que você usa neste caso é a autoridade usual (nome de hospedagem de autoridade + inquilino, comum ou organizações).

### <a name="acquiring-a-token-interactively"></a>Adquirindo um token interativamente
Quando você `AcquireTokenInteractive` chama o método, a experiência do usuário é tipicamente:

1. O usuário insere seu ID de conta.
2. O Azure AD exibe brevemente a mensagem "Levando você para a página da sua organização".
3. O usuário é redirecionado para a página de login do provedor de identidade. A página de login geralmente é personalizada com o logotipo da organização.

As versões AD FS suportadas neste cenário federado são AD FS v2, AD FS v3 (Windows Server 2012 R2) e AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Adquirindo um token usando AcquireTokenByIntegratedAuthentication ou AcquireTokenByUsernamePassword
Ao adquirir um token `AcquireTokenByIntegratedAuthentication` `AcquireTokenByUsernamePassword` usando o ou métodos, MSAL.NET faz com que o provedor de identidade entre em contato com base no nome de usuário.  MSAL.NET recebe um [token SAML 1.1](reference-saml-tokens.md) depois de entrar em contato com o provedor de identidade.  MSAL.NET então fornece o token SAML para o Azure AD como uma afirmação do usuário (semelhante ao [nome do fluxo)](msal-authentication-flows.md#on-behalf-of)para obter de volta um JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL se conecta diretamente ao AD FS
MSAL.NET suporta a conexão com o AD FS 2019, que é compatível com o Open ID Connect e entende PKCE e escopos. Esse suporte requer que um pacote de [serviçoKB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) seja aplicado ao Windows Server. Ao se conectar diretamente ao AD FS, a autoridade que você `https://mysite.contoso.com/adfs/`vai querer usar para construir seu aplicativo é semelhante a .

Atualmente, não há planos para suportar uma conexão direta com:

- AD FS 16, pois não suporta PKCE e ainda usa recursos, não escopo
- AD FS v2, que não é compatível com OIDC.

 Se você precisar suportar cenários que exijam uma conexão direta com o AD FS 2016, use a versão mais recente da Biblioteca de Autenticação de Diretório Ativo do [Azure](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Quando você atualizou seu sistema local para o AD FS 2019, você poderá usar MSAL.NET.

## <a name="next-steps"></a>Próximas etapas

Para o caso federado, consulte [Configurar o login ativo do Azure no comportamento de um aplicativo usando uma diretiva Home Realm Discovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
