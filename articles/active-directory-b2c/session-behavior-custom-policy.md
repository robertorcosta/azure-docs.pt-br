---
title: Configurar o comportamento da sessão usando políticas personalizadas-Azure Active Directory B2C | Microsoft Docs
description: Configure o comportamento da sessão usando políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2f20a4521efe2806c4bc66e4612b99caf84382a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385256"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar o comportamento da sessão usando políticas personalizadas no Azure Active Directory B2C

O gerenciamento de [sessão de SSO (logon único)](session-overview.md) no Azure Active Directory B2C (Azure ad B2C) permite que um administrador controle a interação com um usuário depois que o usuário já tiver se autenticado. Por exemplo, o administrador pode controlar se a seleção de provedores de identidade é exibida ou se os detalhes da conta precisam ser inseridos novamente. Este artigo descreve como definir as configurações de SSO para o Azure AD B2C.

## <a name="session-behavior-properties"></a>Propriedades de comportamento da sessão

Você pode usar as seguintes propriedades para gerenciar sessões de aplicativos da Web:

- **Duração da sessão do aplicativo Web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do usuário após a autenticação bem-sucedida.
    - Padrão = 86400 segundos (1440 minutos).
    - Mínimo (inclusivo) = 900 segundos (15 minutos).
    - Máximo (inclusivo) = 86400 segundos (1440 minutos).
- **Tempo limite da sessão do aplicativo Web** -o [tipo de expiração da sessão](session-overview.md#session-expiry-type), *sem interrupção*ou *absoluto*. 
- **Configuração de logon único** – o [escopo da sessão](session-overview.md#session-scope) do comportamento de logon único (SSO) em vários aplicativos e fluxos de usuário em seu locatário Azure ad B2C. 

## <a name="configure-the-properties"></a>Configurar as propriedades

Para alterar o comportamento da sessão e as configurações de SSO, você adiciona um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty](relyingparty.md).  O elemento **UserJourneyBehaviors** deve vir logo após o **DefaultUserJourney**. O elemento **UserJourneyBehavors** deve ser semelhante a este exemplo:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="single-sign-out"></a>Logout único

### <a name="configure-the-applications"></a>Configurar os aplicativos

Quando você redireciona o usuário para o ponto de extremidade de saída do Azure AD B2C (para os protocolos OAuth2 e SAML), Azure AD B2C limpa a sessão do usuário do navegador.  Para permitir o [logout único](session-overview.md#single-sign-out), defina o `LogoutUrl` do aplicativo no portal do Azure:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Escolha seu diretório Azure AD B2C clicando em sua conta no canto superior direito da página.
1. No menu à esquerda, escolha **Azure ad B2C**, selecione **registros de aplicativo**e, em seguida, selecione seu aplicativo.
1. Selecione **configurações**, selecione **Propriedades**e, em seguida, localize a caixa de texto **URL de logout** . 

### <a name="configure-the-token-issuer"></a>Configurar o emissor do token 

Para dar suporte ao logout único, os perfis técnicos do emissor do token para JWT e SAML devem especificar:

- O nome do protocolo, como `<Protocol Name="OpenIdConnect" />`
- A referência ao perfil técnico da sessão, como `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

O exemplo a seguir ilustra os emissores JWT e token SAML com Logout único:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [sessão do Azure AD B2C](session-overview.md).
