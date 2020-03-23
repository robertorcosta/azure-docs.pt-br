---
title: Adicionar declarações e personalizar a entrada do usuário em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como personalizar a entrada do usuário e adicionar declarações ao percurso de inscrição ou de entrada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56a3478f1c0dbc05eba07a5109f5bb6ba89b79d0
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079874"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Adicionar declarações e personalizar a entrada do usuário usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, você coleta um novo atributo durante a sua jornada de inscrição no Azure Active Directory B2C (Azure AD B2C). Você obterá a cidade dos usuários, o configurará como uma lista suspensa e definirá se ele precisa ser fornecido.

Você pode coletar dados iniciais de seus usuários usando a jornada do usuário de inscrição ou de entrada. Declarações adicionais podem ser coletadas posteriormente usando um percurso do usuário de edição de perfil. A qualquer momento Azure AD B2C coleta informações diretamente do usuário de forma interativa, a estrutura de experiência de identidade usa seu [perfil técnico autodeclarado](self-asserted-technical-profile.md). Neste exemplo, você:

1. Defina uma declaração de "cidade".
1. Peça ao usuário sua cidade.
1. Mantenha a cidade para o perfil do usuário no diretório Azure AD B2C.
1. Leia a declaração de cidade do diretório Azure AD B2C em cada entrada.
1. Retorne a cidade para seu aplicativo de terceira parte confiável após a entrada ou inscrição.  

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada de trabalho para inscrever-se e entrar com contas sociais e locais.

## <a name="define-a-claim"></a>Definir uma declaração

Uma declaração fornece um armazenamento temporário de dados durante uma execução de política de Azure AD B2C. O [esquema de declarações](claimsschema.md) é o local onde você declara suas declarações. Os elementos a seguir são usados para definir a declaração:

- **DisplayName** – Uma cadeia de caracteres que define o rótulo voltado para o usuário.
- [DataType](claimsschema.md#datatype) -o tipo da declaração.
- **UserHelpText** – Ajuda o usuário a entender o que é necessário.
- [Userinputtype](claimsschema.md#userinputtype) – o tipo de controle de entrada, como caixa de texto, seleção de rádio, lista suspensa ou várias seleções.

Abra o arquivo de extensões da política. Por exemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>.

1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema](claimsschema.md) . Se o elemento não existir, adicione-o.
1. Adicione a declaração de cidade ao elemento **ClaimsSchema** .  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Adicionar uma declaração à interface do usuário

Os seguintes perfis técnicos são [autodeclarados](self-asserted-technical-profile.md), invocados quando um usuário espera fornecer entrada:

- **LocalAccountSignUpWithLogonEmail** -fluxo de inscrição de conta local.
- **SelfAsserted-social** -entrada do usuário da primeira vez da conta federada.
- **SelfAsserted-ProfileUpdate** -editar fluxo de perfil.

Para coletar a declaração de cidade durante a inscrição, ela deve ser adicionada como uma declaração de saída ao perfil técnico de `LocalAccountSignUpWithLogonEmail`. Substitua esse perfil técnico no arquivo de extensão. Especifique a lista completa de declarações de saída para controlar a ordem em que as declarações são apresentadas na tela. Localize o elemento **ClaimsProviders**. Adicione um novo ClaimsProviders da seguinte maneira:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

Para coletar a declaração de cidade após a entrada inicial com uma conta federada, ela deve ser adicionada como uma declaração de saída ao perfil técnico de `SelfAsserted-Social`. Para que os usuários da conta local e federada possam editar seus dados de perfil posteriormente, adicione a declaração de saída ao perfil técnico de `SelfAsserted-ProfileUpdate`. Substitua esses perfis técnicos no arquivo de extensão. Especifique a lista completa das declarações de saída para controlar a ordem em que as declarações são apresentadas na tela. Localize o elemento **ClaimsProviders**. Adicione um novo ClaimsProviders da seguinte maneira:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Ler e gravar uma declaração

Os perfis técnicos a seguir são [Active Directory perfis técnicos](active-directory-technical-profile.md), que lêem e gravam dados em Azure Active Directory.  
Use `PersistedClaims` para gravar dados no perfil do usuário e `OutputClaims` para ler dados do perfil do usuário dentro dos respectivos perfis técnicos do Active Directory.

Substitua esses perfis técnicos no arquivo de extensão. Localize o elemento **ClaimsProviders**.  Adicione um novo ClaimsProviders da seguinte maneira:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Incluir uma declaração no token 

Para retornar a declaração de cidade de volta para o aplicativo de terceira parte confiável, adicione uma declaração de saída ao arquivo de <em>`SocialAndLocalAccounts/` **`SignUpOrSignIn.xml`** </em> . A declaração de saída será adicionada ao token após um percurso de usuário bem-sucedido e será enviada para o aplicativo. Modifique o elemento de perfil técnico na seção terceira parte confiável para adicionar a cidade como uma declaração de saída.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário do Azure AD.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Carregar política personalizada** e carregue os dois arquivos de política alterados.
2. Selecione a política de inscrição ou de entrada carregada e clique no botão **Executar agora**.
3. Você deverá conseguir se inscrever usando um endereço de email.

A tela de inscrição deve ser semelhante à seguinte captura:

![Captura de tela da opção de inscrição modificada](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

O token enviado de volta ao seu aplicativo inclui a declaração `city`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o elemento [ClaimsSchema](claimsschema.md) na referência do IEF.
- Saiba como [usar atributos personalizados em uma política de edição de perfil personalizado](custom-policy-custom-attributes.md).
