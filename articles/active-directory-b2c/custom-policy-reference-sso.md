---
title: Gerenciamento único de sessão de inscrição usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como gerenciar sessões de SSO usando políticas personalizadas no Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246026"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gerenciamento de sessão de logon único no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O gerenciamento de sessão de login único (SSO) no Azure Active Directory B2C (Azure AD B2C) permite que um administrador controle a interação com um usuário depois que o usuário já tiver autenticado. Por exemplo, o administrador pode controlar se a seleção de provedores de identidade é exibida ou se os detalhes de conta local precisam ser inseridos novamente. Este artigo descreve como definir as configurações de SSO para o Azure AD B2C.

O gerenciamento de sessão de SSO tem duas partes. A primeira lida com interações do usuário diretamente com o Azure AD B2C e a outra lida com interações do usuário com parceiros externos, como Facebook. O Azure AD B2C não substitui ou ignora as sessões de SSO que podem ser mantidas por parceiros externos. Em vez disso, a rota através do Azure AD B2C para chegar ao partido externo é "lembrada", evitando a necessidade de redirecionar o usuário para selecionar seu provedor de identidade social ou corporativo. A decisão de SSO final permanece com o parceiro externo.

O gerenciamento de sessão de SSO usa a mesma semântica que qualquer outro perfil técnico em políticas personalizadas. Quando uma etapa de orquestração é executada, o perfil técnico associado à etapa é consultado quanto a uma referência `UseTechnicalProfileForSessionManagement`. Se existir uma, o provedor de sessão de SSO referenciado será verificado para ver se o usuário é um participante da sessão. Se sim, o provedor de sessão de SSO será usado para popular novamente a sessão. Da mesma forma, quando a execução de uma etapa de orquestração for concluída, o provedor será usado para armazenar informações na sessão se um provedor de sessão de SSO tiver sido especificado.

O Azure AD B2C definiu vários provedores de sessão de SSO que podem ser usados:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

As classes de gerenciamento de SSO são especificadas usando o elemento `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` de um perfil de técnico.

## <a name="input-claims"></a>Declarações de entrada

O `InputClaims` elemento está vazio ou ausente.

## <a name="persisted-claims"></a>Reivindicações persistidas

As reivindicações que precisam ser devolvidas ao aplicativo ou usadas por pré-condições em etapas subseqüentes, devem ser armazenadas na sessão ou aumentadas por uma leitura do perfil do usuário no diretório. O uso de reivindicações persistentes garante que suas jornadas de autenticação não falhem em reivindicações perdidas. Para adicionar declarações à sessão, use o elemento `<PersistedClaims>` do perfil técnico. Quando o provedor é usado para popular novamente a sessão, as declarações persistentes são adicionadas ao conjunto de declarações.

## <a name="output-claims"></a>Declarações de saída

O `<OutputClaims>` é usado para recuperar reivindicações da sessão.

## <a name="session-providers"></a>Provedores de sessão

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Como o nome indica, este provedor não faz nada. Esse provedor pode ser usado para suprimir o comportamento de SSO para um determinado perfil técnico. O `SM-Noop` perfil técnico a seguir está incluído no [pacote de inicialização de políticas personalizadas](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Esse provedor pode ser usado para armazenar as declarações em uma sessão. Normalmente, esse provedor é referenciado em um perfil técnico usado para gerenciar contas locais. O `SM-AAD` perfil técnico a seguir está incluído no [pacote de inicialização de políticas personalizadas](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

O `SM-MFA` perfil técnico a seguir está incluído no [pacote de inicialização de políticas personalizadas](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`. Este perfil técnico gerencia a sessão de autenticação multifatorial.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Este provedor é usado para suprimir a tela "escolha provedor de identidade". Normalmente, ele é referenciado em um perfil técnico configurado para um provedor de identidade externo, como o Facebook. O `SM-SocialLogin` perfil técnico a seguir está incluído no [pacote de inicialização de políticas personalizadas](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Não | Não usado no momento, pode ser ignorado. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Este provedor é usado para gerenciar as sessões de SAML AD B2C do Azure entre um aplicativo de parte que depende ou um provedor de identidade SAML federado. Ao usar o provedor SSO para armazenar uma sessão `RegisterServiceProviders` de provedor `false`de identidade SAML, o deve ser definido como . O `SM-Saml-idp` seguinte perfil técnico é utilizado pelo [perfil técnico SAML](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Ao usar o provedor para armazenar a sessão B2C SAML, o `RegisterServiceProviders` deve ser definido como `true`. O logoff da sessão de SAML requer que `SessionIndex` e `NameID` sejam concluídos.

O `SM-Saml-idp` seguinte perfil técnico é usado pelo [perfil técnico do emissor SAML](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição|
| --- | --- | --- |
| IncludeSessionIndex | Não | Não usado no momento, pode ser ignorado.|
| RegisterServiceProviders | Não | Indica que o provedor deve registrar todos os provedores de serviço SAML que emitiram uma declaração. Valores possíveis: `true` (padrão) ou `false`.|



