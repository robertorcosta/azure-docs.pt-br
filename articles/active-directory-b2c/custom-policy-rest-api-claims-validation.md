---
title: AAPI REST reivindica trocas como validação
titleSuffix: Azure AD B2C
description: Um passo a passo para criar uma jornada de usuário Azure AD B2C que interage com os serviços RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330829"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Passo a passo: Integre as trocas de reclamações da API REST em sua jornada de usuário Azure AD B2C para validar a entrada do usuário

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O IEF (Identity Experience Framework, estrutura de experiência de identidade) que sustenta o Azure Active Directory B2C (Azure AD B2C) permite que os desenvolvedores de identidade integrem uma interação com uma API RESTful em uma jornada do usuário.  No final deste passo a passo, você poderá criar uma jornada de usuário Azure AD B2C que interage com [os serviços RESTful](custom-policy-rest-api-intro.md) para validar a entrada do usuário.

Neste cenário, adicionaremos a capacidade de os usuários inserirem um número de fidelidade na página de inscrição Azure AD B2C. Vamos validar se essa combinação de e-mail e número de fidelidade é mapeada para um código promocional enviando esses dados para uma API REST. Se a API REST encontrar um código promocional para este usuário, ele será devolvido ao Azure AD B2C. Finalmente, o código promocional será inserido nas reivindicações do token para o aplicativo consumir.

Você também pode criar a interação como uma etapa de orquestração. Isso é adequado quando a API REST não estará validando dados na tela e sempre retornará reclamações. Para obter mais informações, consulte [Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como uma etapa de orquestração](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.
- Saiba como integrar as trocas de [reclamações da API REST em sua política personalizada Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Prepare um ponto final da API REST

Para este passo a passo, você deve ter uma API REST que valida se um endereço de e-mail está registrado em seu sistema back-end com um ID de fidelidade. Se registrado, a API REST deve retornar um código de promoção de registro, que o cliente pode usar para comprar mercadorias dentro de seu aplicativo. Caso contrário, a API REST deve retornar uma mensagem de erro HTTP 409: "O ID de fidelidade '{loyalty ID}' não está associado ao endereço de e-mail '{email}'."

O código JSON a seguir ilustra os dados que o Azure AD B2C enviará para o ponto final da API REST. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Uma vez que sua API REST valide os dados, ela deve retornar um HTTP 200 (Ok), com os seguintes dados JSON:

```json
{
    "promoCode": "24534"
}
```

Se a validação falhar, a API REST deve retornar um `userMessage` HTTP 409 (Conflito), com o elemento JSON. A IEF `userMessage` espera a alegação de que a API REST retorna. Esta reclamação será apresentada como uma string para o usuário se a validação falhar.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

A configuração do ponto final da API REST está fora do escopo deste artigo. Criamos uma amostra [de Funções Azure.](https://docs.microsoft.com/azure/azure-functions/functions-reference) Você pode acessar o código completo de função Azure no [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definir reivindicações

Uma reclamação fornece armazenamento temporário de dados durante a execução de uma diretiva Azure AD B2C. Você pode declarar reivindicações dentro da seção [de sinistros.](claimsschema.md) 

1. Abra o arquivo de extensões da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione as seguintes reivindicações ao elemento **ClaimsSchema.**  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Configure o perfil técnico da API RESTful 

Um [perfil técnico tranquilo](restful-technical-profile.md) fornece suporte para interseção ao seu próprio serviço RESTful. O Azure AD B2C envia dados para `InputClaims` o serviço RESTful em uma coleta e recebe dados de volta em uma `OutputClaims` coleta. Encontre o elemento **Provedores de Sinistros** e adicione um novo provedor de sinistros da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Neste exemplo, `userLanguage` o será enviado para `lang` o serviço REST como dentro da carga útil JSON. O valor `userLanguage` da declaração contém o ID de linguagem de usuário atual. Para obter mais informações, consulte [a reivindicação resolver](claim-resolver-overview.md).

Os comentários `AuthenticationType` `AllowInsecureAuthInProduction` acima e especificar alterações que você deve fazer quando você se move para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, consulte [API Secure RESTful](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Validar a entrada do usuário

Para obter o número de fidelidade do usuário durante a inscrição, você deve permitir que o usuário insira esses dados na tela. Adicione a reivindicação de saída **de fidelId** à página de inscrição adicionando-a `OutputClaims` ao elemento da seção de perfil técnico de inscrição existente. Especifique toda a lista de reivindicações de saída para controlar a ordem que as reivindicações são apresentadas na tela.  

Adicione a referência do perfil técnico de validação ao `REST-ValidateProfile`perfil técnico de inscrição, que chama de . O novo perfil técnico de validação será `<ValidationTechnicalProfiles>` adicionado ao topo da coleção definida na política base. Esse comportamento significa que somente após a validação bem-sucedida, o Azure AD B2C passa a criar a conta no diretório.   

1. Localize o elemento **ClaimsProviders**. Adicione um novo provedor de sinistros da seguinte forma:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Inclua uma reivindicação no token 

Para devolver a reivindicação de código promocional ao aplicativo de <em> `SocialAndLocalAccounts/` </em> parte que depende, adicione uma reivindicação de saída ao arquivo. A reivindicação de saída permitirá que a reivindicação seja adicionada ao token após uma jornada de usuário bem-sucedida e será enviada para o aplicativo. Modifique o elemento de perfil técnico dentro `promoCode` da seção de parte de dependência para adicionar o como uma reivindicação de saída.
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Faça login no [portal Azure](https://portal.azure.com).
1. Certifique-se de que está usando o diretório que contém o inquilino Azure AD selecionando o filtro **de assinatura Diretório +** no menu superior e escolhendo o diretório que contém o inquilino Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Selecione **'Enviar diretiva personalizada'** e, em seguida, faça upload dos arquivos de diretiva que você alterou: *TrustFrameworkExtensions.xml*e *SignUpOrSignin.xml*. 
1. Selecione a política de inscrição ou de entrada carregada e clique no botão **Executar agora**.
1. Você deverá conseguir se inscrever usando um endereço de email.
1. Clique no link **Inscreva-se agora.**
1. No **ID de sua lealdade,** tipo 1234, e clique em **Continuar**. Neste ponto, você deve receber uma mensagem de erro de validação.
1. Mude para outro valor e clique **em Continuar**.
1. O token enviado de volta ao seu aplicativo inclui a declaração `promoCode`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber como proteger suas APIs, consulte os seguintes artigos:

- [Passo a passo: integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como uma etapa de orquestração](custom-policy-rest-api-claims-exchange.md)
- [Proteja sua API RESTful](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)
