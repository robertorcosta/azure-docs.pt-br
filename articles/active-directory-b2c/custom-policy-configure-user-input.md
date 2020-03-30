---
title: Adicionar reivindicações e personalizar a entrada do usuário em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como personalizar a entrada do usuário e adicionar declarações ao percurso de inscrição ou de entrada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473669"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Adicionar declarações e personalizar a entrada do usuário usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, você coleta um novo atributo durante sua jornada de inscrição no Azure Active Directory B2C (Azure AD B2C). Você obterá a cidade dos usuários, configurá-la como um drop-down e definir se ela é necessária para ser fornecida.

> [!NOTE]
> Esta amostra usa a reivindicação incorporada 'cidade'. Em vez disso, você pode escolher um dos [atributos integrados AD B2C](user-profile-attributes.md) suportados do Azure Ou um atributo personalizado. Para usar um atributo personalizado, [habilite atributos personalizados em sua política](custom-policy-custom-attributes.md). Para usar um atributo interno ou personalizado diferente, substitua 'cidade' pelo atributo de sua escolha, por exemplo, o trabalho de atributo *incorporadoTitle* ou um atributo personalizado como *extension_loyaltyId*.  

Você pode coletar dados iniciais de seus usuários usando a jornada de entrada ou login do usuário. Declarações adicionais podem ser coletadas posteriormente usando um percurso do usuário de edição de perfil. Sempre que o Azure AD B2C coleta informações diretamente do usuário de forma interativa, o Identity Experience Framework usa seu [perfil técnico auto-afirmado](self-asserted-technical-profile.md). Nesta amostra, você:

1. Defina uma reivindicação de "cidade". 
1. Pergunte ao usuário por sua cidade.
1. Persista a cidade até o perfil do usuário no diretório Azure AD B2C.
1. Leia a reivindicação da cidade do diretório Azure AD B2C em cada login.
1. Devolva a cidade ao seu pedido de festa de dependência após o login ou inscrição.  

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada de trabalho para se inscrever e se inscrever com contas sociais e locais.

## <a name="define-a-claim"></a>Defina uma reivindicação

Uma reclamação fornece um armazenamento temporário de dados durante a execução de uma diretiva Azure AD B2C. O [esquema de sinistros](claimsschema.md) é o lugar onde você declara suas reivindicações. Os elementos a seguir são usados para definir a declaração:

- **DisplayName** – Uma cadeia de caracteres que define o rótulo voltado para o usuário.
- [DataType](claimsschema.md#datatype) - O tipo de reclamação.
- **UserHelpText** – Ajuda o usuário a entender o que é necessário.
- [UserInputType](claimsschema.md#userinputtype) - O tipo de controle de entrada, como caixa de texto, seleção de rádio, lista de parada ou várias seleções.

Abra o arquivo de extensões da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.

1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione a reivindicação da cidade ao elemento **ClaimsSchema.**  

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

## <a name="add-a-claim-to-the-user-interface"></a>Adicione uma reivindicação à interface do usuário

Os seguintes perfis técnicos são [auto-afirmados,](self-asserted-technical-profile.md)invocados quando se espera que um usuário forneça informações:

- **LocalAccountSignUpWithLogonEmail** - Fluxo de inscrição da conta local.
- **SelfAsserted-Social** - Conta federada pela primeira vez no login do usuário.
- **SelfAsserted-ProfileUpdate** - Editar fluxo de perfil.

Para recolher a reclamação da cidade durante a inscrição, ele `LocalAccountSignUpWithLogonEmail` deve ser adicionado como uma reivindicação de saída para o perfil técnico. Anular esse perfil técnico no arquivo de extensão. Especifique toda a lista de reivindicações de saída para controlar a ordem que as reivindicações são apresentadas na tela. Localize o elemento **ClaimsProviders**. Adicione um novo Provedor de Sinistros da seguinte forma:

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

Para recolher a reivindicação da cidade após o login inicial com uma conta `SelfAsserted-Social` federada, ele deve ser adicionado como uma reivindicação de saída para o perfil técnico. Para que os usuários de contas locais e federadas possam editar `SelfAsserted-ProfileUpdate` seus dados de perfil mais tarde, adicione a reivindicação de saída ao perfil técnico. Anular esses perfis técnicos no arquivo de extensão. Especifique a lista completa das reivindicações de saída para controlar a ordem em que as reivindicações são apresentadas na tela. Localize o elemento **ClaimsProviders**. Adicione um novo Provedor de Sinistros da seguinte forma:

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

## <a name="read-and-write-a-claim"></a>Leia e escreva uma reivindicação

Os seguintes perfis técnicos são [perfis técnicos do Active Directory,](active-directory-technical-profile.md)que lêem e escrevem dados no Azure Active Directory.  
Use `PersistedClaims` para gravar dados no `OutputClaims` perfil do usuário e ler dados do perfil do usuário dentro dos respectivos perfis técnicos do Active Directory.

Anular esses perfis técnicos no arquivo de extensão. Localize o elemento **ClaimsProviders**.  Adicione um novo Provedor de Sinistros da seguinte forma:

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

## <a name="include-a-claim-in-the-token"></a>Inclua uma reivindicação no token 

Para devolver a reivindicação da cidade ao aplicativo de parte <em> `SocialAndLocalAccounts/` </em> que depende, adicione uma reivindicação de saída ao arquivo. A reivindicação de saída será adicionada ao token após uma jornada de usuário bem-sucedida e será enviada para o aplicativo. Modifique o elemento de perfil técnico dentro da seção de parte de dependência para adicionar a cidade como uma reivindicação de saída.
 
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

1. Faça login no [portal Azure](https://portal.azure.com).
2. Certifique-se de que está usando o diretório que contém o inquilino Azure AD selecionando o filtro **de assinatura Diretório +** no menu superior e escolhendo o diretório que contém o inquilino Azure AD.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Carregar política personalizada** e carregue os dois arquivos de política alterados.
2. Selecione a política de inscrição ou de entrada carregada e clique no botão **Executar agora**.
3. Você deverá conseguir se inscrever usando um endereço de email.

A tela de inscrição deve ser semelhante à seguinte captura de tela:

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
- Aprenda a [usar atributos personalizados em uma política de edição de perfil personalizada](custom-policy-custom-attributes.md).
