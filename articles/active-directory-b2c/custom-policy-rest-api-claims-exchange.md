---
title: Trocas de reclamações da API REST - Azure Active Directory B2C
description: Adicionar trocas de reclamações da API REST às políticas personalizadas no Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330715"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Passo a passo: Adicionar trocas de reclamações da API REST a políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) permite que os desenvolvedores de identidade integrem uma interação com uma API RESTful em uma jornada de usuário. Ao final deste passo a passo, você poderá criar uma jornada de usuário Azure AD B2C que interage com [os serviços RESTful](custom-policy-rest-api-intro.md).

Nesse cenário, enriquecemos os dados de token do usuário, integrando-se a um fluxo de trabalho corporativo de linha de negócios. Durante o login ou login com conta local ou federada, o Azure AD B2C invoca uma API REST para obter os dados de perfil estendidos do usuário a partir de uma fonte de dados remota. Nesta amostra, o Azure AD B2C envia o identificador exclusivo do usuário, o objectId. A API REST retorna então o saldo da conta do usuário (um número aleatório). Use essa amostra como ponto de partida para se integrar ao seu próprio sistema de CRM, banco de dados de marketing ou qualquer fluxo de trabalho de linha de negócios.

Você também pode projetar a interação como um perfil técnico de validação. Isso é adequado quando a API REST estará validando dados na tela e retornando reivindicações. Para obter mais informações, consulte [Passo a passo: Integre as trocas de reclamações da API REST em sua jornada de usuário Azure AD B2C para validar a entrada do usuário](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.
- Saiba como integrar as trocas de [reclamações da API REST em sua política personalizada Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Prepare um ponto final da API REST

Para este passo a passo, você deve ter uma API REST que valide se o objeto AZure AD B2C do usuário está registrado no seu sistema back-end. Se registrado, a API REST devolve o saldo da conta de usuário. Caso contrário, a API REST registra a nova conta no `50.00`diretório e devolve o saldo inicial .

O código JSON a seguir ilustra os dados que o Azure AD B2C enviará para o ponto final da API REST. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

Uma vez que sua API REST valide os dados, ela deve retornar um HTTP 200 (Ok), com os seguintes dados JSON:

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Configure o perfil técnico da API RESTful 

Um [perfil técnico tranquilo](restful-technical-profile.md) fornece suporte para interagir com seu próprio serviço RESTful. O Azure AD B2C envia dados para `InputClaims` o serviço RESTful em uma coleta e recebe dados de volta em uma `OutputClaims` coleta. Encontre o elemento **ClaimsProviders** em seu <em>**`TrustFrameworkExtensions.xml`**</em> arquivo e adicione um novo provedor de sinistros da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Neste exemplo, `userLanguage` o será enviado para `lang` o serviço REST como dentro da carga útil JSON. O valor `userLanguage` da declaração contém o ID de linguagem de usuário atual. Para obter mais informações, consulte [a reivindicação resolver](claim-resolver-overview.md).

Os comentários `AuthenticationType` `AllowInsecureAuthInProduction` acima e especificar alterações que você deve fazer quando você se move para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, consulte [API Secure RESTful](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Adicione um passo de orquestração

[As jornadas dos usuários](userjourneys.md) especificam caminhos explícitos através dos quais uma política permite que um aplicativo de parte que depende obtenha as reivindicações desejadas para um usuário. Um percurso do usuário é representado como uma sequência de orquestração que deve ser seguida para ter uma transação bem-sucedida. Você pode adicionar ou subtrair etapas de orquestração. Neste caso, você adicionará uma nova etapa de orquestração que é usada para aumentar as informações fornecidas ao aplicativo após a inscrição ou login do usuário através da chamada DA API REST.

1. Abra o arquivo base da sua política. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Pesquise o elemento `<UserJourneys>`. Copie todo o elemento e, em seguida, exclua-o.
1. Abra o arquivo de extensões da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Cole o `<UserJourneys>` arquivo de extensões, após `<ClaimsProviders>` o fechamento do elemento.
1. Localize `<UserJourney Id="SignUpOrSignIn">`o e adicione o seguinte passo de orquestração antes do último.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refatorar o último passo `Order` de `8`orquestração mudando o para . Seus dois últimos passos de orquestração devem ser os seguintes:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Repita as duas últimas etapas para as jornadas de usuário **ProfileEdit** e **PasswordReset.**


## <a name="include-a-claim-in-the-token"></a>Inclua uma reivindicação no token 

Para devolver `balance` a reclamação ao aplicativo da parte de <em> `SocialAndLocalAccounts/` </em> dependência, adicione uma reivindicação de saída ao arquivo. A adição de uma reivindicação de saída emitirá a reclamação no token após uma jornada de usuário bem-sucedida e será enviada para o aplicativo. Modifique o elemento de perfil técnico `balance` dentro da seção de parte de confiar para adicionar como uma reivindicação de saída.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Repita esta etapa para as jornadas de usuário **ProfileEdit.xml**e **PasswordReset.xml.**

Salve os arquivos que você alterou: *TrustFrameworkBase.xml*e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml,* *ProfileEdit.xml*e *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Faça login no [portal Azure](https://portal.azure.com).
1. Certifique-se de que está usando o diretório que contém o inquilino Azure AD selecionando o filtro **de assinatura Diretório +** no menu superior e escolhendo o diretório que contém o inquilino Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Selecione **'Enviar política personalizada'** e, em seguida, carregue os arquivos de diretiva que você alterou: *TrustFrameworkBase.xml*e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml,* *ProfileEdit.xml*e *PasswordReset.xml*. 
1. Selecione a política de inscrição ou de entrada carregada e clique no botão **Executar agora**.
1. Você deve ser capaz de se inscrever usando um endereço de e-mail ou uma conta do Facebook.
1. O token enviado de volta ao seu aplicativo inclui a declaração `balance`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Próximas etapas


## <a name="next-steps"></a>Próximas etapas

Para saber como proteger suas APIs, consulte os seguintes artigos:

- [Passo a passo: integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como uma etapa de orquestração](custom-policy-rest-api-claims-exchange.md)
- [Proteja sua API RESTful](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)
