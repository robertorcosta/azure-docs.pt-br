---
title: Defina um perfil técnico RESTful em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico RESTful em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332603"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico RESTful em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) oferece suporte para integrar seu próprio serviço RESTful. O Azure AD B2C envia dados para o serviço RESTful na entrada de uma coleção de declarações e recebe dados de volta em uma coleção de declarações de saída. Para obter mais informações, consulte [Integrar as trocas de reclamações da API no azure AD B2C](custom-policy-rest-api-intro.md)(  

## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `Proprietary`. O atributo **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo usado pelo Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

O exemplo a seguir mostra um perfil técnico RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas para a API REST. Você também pode mapear o nome de sua declaração para o nome definido na API REST. O exemplo a seguir mostra o mapeamento entre sua política e a API REST. A declaração **givenName** é enviada para a API REST como **firstName**, enquanto **surname** é enviado como **lastName**. O A declaração de **email** é definida como está.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

O elemento **InputClaimsTransformations** elemento pode conter uma coleção de elementos **InputClaimsTransformation** elementos usados para modificar as declarações de entrada ou gerar novas antes do envio à API REST.

## <a name="send-a-json-payload"></a>Envie uma carga útil JSON

O perfil técnico da API REST permite que você envie uma carga json complexa para um ponto final.

Para enviar uma carga json complexa:

1. Construa sua carga útil JSON com a transformação de [sinistros GenerateJson.](json-transformations.md)
1. No perfil técnico da API REST:
    1. Adicione uma transformação de reivindicações de entrada com uma referência à transformação de `GenerateJson` sinistros.
    1. Defina `SendClaimsIn` a opção de metadados para`body`
    1. Defina `ClaimUsedForRequestPayload` a opção de metadados para o nome da reclamação que contém a carga útil JSON.
    1. Na reclamação de entrada, adicione uma referência à reclamação de entrada que contém a carga útil JSON.

O exemplo `TechnicalProfile` a seguir envia um e-mail de verificação usando um serviço de e-mail de terceiros (neste caso, sendgrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pela API REST. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido na API REST. Você também pode incluir declarações que não são retornadas pelo provedor de identidade da API REST, desde que defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

O exemplo a seguir mostra a declaração retornada pela API REST:

- A declaração **MembershipId** mapeada para o nome da declaração **loyaltyNumber**.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A declaração **loyaltyNumberIsNew** que tem um valor padrão definido como `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ServiceUrl | Sim | A URL do ponto de extremidade da API REST. |
| AuthenticationType | Sim | O tipo de autenticação que está sendo executada pelo provedor de declarações RESTful. Valores possíveis: `None`, `Basic`, `Bearer` ou `ClientCertificate`. O valor `None` indica que a API REST não é anônima. O valor `Basic` indica que a API REST está protegida com a autenticação Básica HTTP. Somente usuários verificados, incluindo Azure AD B2C, podem acessar a API. O `ClientCertificate` valor (recomendado) indica que a API REST restringe o acesso usando a autenticação do certificado do cliente. Somente os serviços que possuem os certificados apropriados, por exemplo, o Azure AD B2C, podem acessar sua API. O `Bearer` valor indica que a API REST restringe o acesso usando o token Cliente OAuth2 Bearer. |
| PermitirinsecureAuthInProduction| Não| Indica se `AuthenticationType` o pode `none` ser definido`DeploymentMode` para no ambiente de `Production`produção (da [TrustFrameworkPolicy](trustframeworkpolicy.md) está definida ou não especificada). Valores possíveis: verdadeiros ou falsos (padrão). |
| SendClaimsIn | Não | Especifica como as declarações de entrada são enviadas para o provedor de declarações RESTful. Valores possíveis: `Body` (padrão), `Form`, `Header` ou `QueryString`. O valor `Body` é a declaração de entrada enviada no corpo da solicitação no formato JSON. O valor `Form` é a declaração de entrada enviada no corpo da solicitação em um formato de valor de chave separado de e comercial '&'. O valor `Header` é a declaração de entrada enviada no cabeçalho da solicitação. O valor `QueryString` é a declaração de entrada enviada na cadeia de caracteres de consulta da solicitação. Os verbos HTTP invocados por cada um são os seguintes:<br /><ul><li>`Body`: CORREIO</li><li>`Form`: CORREIO</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | Não | Não usado no momento, pode ser ignorado. |
| ReclamaçãoUsedForRequestPayLoad| Não | Nome de uma reivindicação de string que contém a carga útil a ser enviada para a API REST. |
| DebugMode | Não | Executa o perfil técnico no modo de depuração. Valores `true`possíveis: `false` , ou (padrão). No modo de depuração, a API REST pode retornar mais informações. Consulte a [seção Mensagem de erro Retornando.](#returning-error-message) |
| IncludeClaimResolveingInClaimshandling  | Não | Para reclamações de entrada e saída, especifica se a [resolução de sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores `true`possíveis: `false`  , ou (padrão). Se você quiser usar um resolver sinistros no `true`perfil técnico, defina isso como . |
| ResolveJsonPathsInJsonTokens  | Não | Indica se o perfil técnico resolve os caminhos JSON. Valores `true`possíveis: `false` , ou (padrão). Use esses metadados para ler dados de um elemento JSON aninhado. Em uma [OutputClaim](technicalprofiles.md#outputclaims) `PartnerClaimType` , defina o elemento de caminho JSON que deseja fazer. Por exemplo: `firstName.localized` `data.0.to.0.email`, ou .|
| UseClaimAsBearerToken| Não| O nome da reivindicação que contém o token do portador.|

## <a name="cryptographic-keys"></a>Chaves de criptografia

Se o tipo de autenticação for definido como `None`, o elemento **CryptographicKeys** não será usado.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Se o tipo de autenticação for definido como `Basic`, o elemento **CryptographicKeys** conterá os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Sim | O nome de usuário usado para autenticar. |
| BasicAuthenticationPassword | Sim | A senha usada para autenticar. |

O exemplo a seguir mostra um perfil técnico com a autenticação Básica:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se o tipo de autenticação for definido como `ClientCertificate`, o elemento **CryptographicKeys** conterá o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClientCertificate | Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para autenticar. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se o tipo de autenticação for definido como `Bearer`, o elemento **CryptographicKeys** conterá o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Autenticação do portadorToken | Não | O OAuth 2.0 Bearer Token. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Mensagem de erro retornada

A API REST talvez precise retornar uma mensagem de erro, como "O usuário não foi encontrado no sistema CRM". Se ocorrer um erro, a API REST deve retornar uma mensagem de erro HTTP 4xx, como, 400 (má solicitação) ou 409 (conflito) código de status de resposta. O corpo de resposta contém mensagem de erro formatada em JSON:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| version | Sim | Sua versão aPI REST. Por exemplo: 1.0.1 |
| status | Sim | Deve ser 409. |
| código | Não | Um código de erro do provedor de ponto de extremidade RESTful, que é exibido quando `DebugMode` está habilitado. |
| requestId | Não | Um identificador de solicitação do provedor de ponto de extremidade RESTful, que é exibido quando `DebugMode` está habilitado. |
| userMessage | Sim | Uma mensagem de erro que é mostrada ao usuário. |
| developerMessage | Não | A descrição detalhada do problema e como corrigi-lo, o que é exibido quando `DebugMode` está habilitado. |
| moreInfo | Não | Um URI que aponta para informações adicionais, que são exibidas quando `DebugMode` está habilitado. |


O exemplo a seguir mostra uma classe C# que retorna uma mensagem de erro:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Próximas etapas

Veja os seguintes artigos para exemplos de uso de um perfil técnico RESTful:

- [Integre as trocas de reclamações da API REST em sua política personalizada Azure AD B2C](custom-policy-rest-api-intro.md)
- [Passo a passo: Integre as trocas de reclamações da API REST em sua jornada de usuário Azure AD B2C como validação da entrada do usuário](custom-policy-rest-api-claims-validation.md)
- [Passo a passo: Adicionar trocas de reclamações da API REST a políticas personalizadas no Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteja seus serviços de API REST](secure-rest-api.md)

