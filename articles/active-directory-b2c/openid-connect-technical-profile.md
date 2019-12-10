---
title: Definir um perfil técnico do OpenID Connect em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico do OpenID Connect em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fdb925f820cad79fe68e7082f4ed63292a7d9444
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951081"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do OpenID Connect em uma política personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para o provedor de identidade do protocolo [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . O OpenID Connect 1.0 define uma camada de identidade com base em OAuth 2.0 e representa a estado de última geração em protocolos de autenticação modernos. Com um perfil técnico do OpenID Connect, você pode federar com um provedor de identidade baseado em OpenID Connect, como o Azure AD. A Federação com um provedor de identidade permite que os usuários entrem com suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `OpenIdConnect`. Por exemplo, o protocolo para o perfil técnico **MSA-OIDC** é `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Declarações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** não são necessários. Mas talvez você queira enviar parâmetros adicionais para seu provedor de identidade. O exemplo a seguir adiciona o parâmetro de cadeia de caracteres de consulta **domain_hint** com o valor de `contoso.com` à solicitação de autorização.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade do OpenID Connect. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade, desde que defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade de Conta Microsoft:

- A **subdeclaração que** é mapeada para a Declaração **issuerUserId** .
- A declaração **name** que é mapeada para a declaração **displayName**.
- O **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A declaração **identityProvider** que contém o nome do provedor de identidade.
- A declaração **authenticationSource** com um valor padrão de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | obrigatórios | Descrição |
| --------- | -------- | ----------- |
| client_id | SIM | O identificador do aplicativo do provedor de identidade. |
| IdTokenAudience | Não | O público-alvo do id_token. Se for especificado, o Azure AD B2C verificará se o token está em uma declaração retornada pelo provedor de identidade e é igual ao especificado. |
| METADATA | SIM | Uma URL que aponta para um documento de configuração JSON formatado segundo a especificação de descoberta do OpenID Connect, que também é conhecido como um ponto de extremidade de configuração do openid bem conhecido. |
| ProviderName | Não | O nome do provedor de identidade. |
| response_types | Não | O tipo de resposta de acordo com a especificação do OpenID Connect Core 1.0. Valores possíveis: `id_token`, `code` ou `token`. |
| response_mode | Não | O método que o provedor de identidade usa para enviar o resultado de volta ao Azure AD B2C. Valores possíveis: `query`, `form_post` (padrão) ou `fragment`. |
| scope | Não | O escopo da solicitação que é definido de acordo com a especificação do OpenID Connect Core 1,0. Como `openid`, `profile` e `email`. |
| HttpBinding | Não | A associação HTTP esperada para o token de acesso e pontos de extremidade do token de declarações. Os valores possíveis são `GET` ou `POST`.  |
| ValidTokenIssuerPrefixes | Não | Uma chave que pode ser usada para entrar em cada um dos locatários ao usando um provedor de identidade multilocatário como o Azure Active Directory. |
| UsePolicyInRedirectUri | Não | Indica se deve ser usada uma política ao criar o URI de redirecionamento. Quando você configura seu aplicativo no provedor de identidade, precisa especificar o URI de redirecionamento. O URI de redirecionamento aponta para Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  Se você especificar `false`, precisará adicionar um URI de redirecionamento a cada política que você usar. Por exemplo: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Não | Indica se uma solicitação para um serviço externo deverá ser marcada como uma falha se o código de status Http estiver no intervalo 5xx. O padrão é `false`. |
| DiscoverMetadataByTokenIssuer | Não | Indica se os metadados OIDC devem ser descobertos usando o emissor no token JWT. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | obrigatórios | Descrição |
| --------- | -------- | ----------- |
| client_secret | SIM | O segredo do cliente do aplicativo do provedor de identidade. A chave de criptografia será necessária apenas se os metadados **response_types** estiverem definidos como `code`. Nesse caso, o Azure AD B2C faz outra chamada para trocar o código de autorização para um token de acesso. Se os metadados forem definidos como `id_token`, você poderá omitir a chave de criptografia.  |

## <a name="redirect-uri"></a>URI de redirecionamento

Ao configurar o URI de redirecionamento do seu provedor de identidade, insira `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Certifique-se de substituir `{your-tenant-name}` pelo nome do locatário. O URI de redirecionamento deve ser todo em letras minúsculas.

Exemplos:

- [Adicionar MSA (Conta Microsoft) como um provedor de identidade usando políticas personalizadas](active-directory-b2c-custom-setup-msa-idp.md)
- [Entrar usando contas do Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Permitir que os usuários entrem em um provedor de identidade multilocatário do Azure AD usando políticas personalizadas](active-directory-b2c-setup-commonaad-custom.md)
