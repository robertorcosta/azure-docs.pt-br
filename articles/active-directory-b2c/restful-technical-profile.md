---
title: Definir um perfil técnico RESTful em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico RESTful em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b83a6bacf1c6e392db9dfc65fd737ea28416a6b5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183815"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico RESTful em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para seu próprio serviço RESTful. O Azure AD B2C envia dados para o serviço RESTful na entrada de uma coleção de declarações e recebe dados de volta em uma coleção de declarações de saída. Com a integração de serviço RESTful, você pode:

- **Validar dados de entrada do usuário** – Impede que dados malformados persistam no Azure AD B2C. Se o valor do usuário não for válido, o serviço RESTful retornará uma mensagem de erro que instrui o usuário a fornecer uma entrada. Por exemplo, você pode verificar se o endereço de email fornecido pelo usuário existe no banco de dados do cliente.
- **Substituir declarações de entrada** – permite reformatar os valores em declarações de entrada. Por exemplo, se um usuário inserir o nome com todas as letras maiúsculas ou minúsculas, você poderá formatar o nome usando somente a primeira letra maiúscula.
- **Enriquecer dados do usuário** – permite que você integre ainda mais com aplicativos de linha de negócios. Por exemplo, seu serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário ao Azure AD B2C. As declarações de retornadas podem ser armazenadas, avaliadas nas próximas etapas de orquestração ou incluídas no token de acesso.
- **Executar lógica de negócios personalizada** – permite enviar notificações por push, atualizar bancos de dados corporativos, executar um processo de migração de usuário, gerenciar permissões e auditar bancos de dados, além de realizar diversas outras ações.

Sua política pode enviar declarações de entrada para a API REST. A API REST também podem retornar declarações de saída que você pode usar mais tarde em sua política, ou pode gerar uma mensagem de erro. É possível criar a integração com os serviços RESTful das seguintes maneiras:

- **Perfil técnico de validação** – um perfil técnico de validação chama o serviço RESTful. O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário continue. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página autodeclarada e retornada em declarações de saída.
- **Troca de declarações** – é feita uma chamada para o serviço RESTful por meio de uma etapa de orquestração. Nesse cenário, não há interface do usuário para renderizar a mensagem de erro. Se a API REST retornar um erro, o usuário será redirecionado de volta para o aplicativo de terceira parte confiável com a mensagem de erro.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo usado pelo Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

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

## <a name="send-a-json-payload"></a>Enviar uma carga JSON

O perfil técnico da API REST permite enviar uma carga JSON complexa para um ponto de extremidade.

Para enviar uma carga JSON complexa:

1. Crie sua carga JSON com a transformação declarações [GenerateJson](json-transformations.md) .
1. No perfil técnico da API REST:
    1. Adicione uma transformação de declarações de entrada com uma referência à transformação declarações de `GenerateJson`.
    1. Defina a opção de metadados `SendClaimsIn` como `body`
    1. Defina a opção de metadados `ClaimUsedForRequestPayload` como o nome da declaração que contém a carga JSON.
    1. Na declaração de entrada, adicione uma referência à declaração de entrada que contém a carga JSON.

O exemplo a seguir `TechnicalProfile` envia um email de verificação usando um serviço de email de terceiros (neste caso, SendGrid).

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

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| ServiceUrl | Sim | A URL do ponto de extremidade da API REST. |
| AuthenticationType | Sim | O tipo de autenticação que está sendo executada pelo provedor de declarações RESTful. Valores possíveis: `None`, `Basic`, `Bearer` ou `ClientCertificate`. O valor `None` indica que a API REST não é anônima. O valor `Basic` indica que a API REST está protegida com a autenticação Básica HTTP. Somente usuários verificados, incluindo Azure AD B2C, podem acessar a API. O valor `ClientCertificate` (recomendado) indica que a API REST restringe o acesso usando a autenticação de certificado do cliente. Somente os serviços que têm os certificados apropriados, por exemplo Azure AD B2C, podem acessar sua API. O valor `Bearer` indica que a API REST restringe o acesso usando o token de portador OAuth2 do cliente. |
| SendClaimsIn | Não | Especifica como as declarações de entrada são enviadas para o provedor de declarações RESTful. Valores possíveis: `Body` (padrão), `Form`, `Header` ou `QueryString`. O valor `Body` é a declaração de entrada enviada no corpo da solicitação no formato JSON. O valor `Form` é a declaração de entrada enviada no corpo da solicitação em um formato de valor de chave separado de e comercial '&'. O valor `Header` é a declaração de entrada enviada no cabeçalho da solicitação. O valor `QueryString` é a declaração de entrada enviada na cadeia de caracteres de consulta da solicitação. Os verbos HTTP invocados por cada um são os seguintes:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: obter</li><li>`QueryString`: obter</li></ul> |
| ClaimsFormat | Não | Especifica o formato para as declarações de saída. Valores possíveis: `Body` (padrão), `Form`, `Header` ou `QueryString`. O valor `Body` é a declaração de saída enviada no corpo da solicitação no formato JSON. O valor `Form` é a declaração de saída enviada no corpo da solicitação em um formato de valor de chave separado de e comercial '&'. O valor `Header` é a declaração de saída enviada no cabeçalho da solicitação. O valor `QueryString` é a declaração de saída enviada na cadeia de consulta de solicitação. |
| ClaimUsedForRequestPayload| Não | Nome de uma declaração de cadeia de caracteres que contém a carga a ser enviada para a API REST. |
| DebugMode | Não | Executa o perfil técnico no modo de depuração. Valores possíveis: `true`ou `false` (padrão). No modo de depuração, a API REST pode retornar mais informações. Consulte a seção [retornando mensagem de erro](#returning-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | Não | Para declarações de entrada e saída, especifica se a [resolução de declarações](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true`ou `false` (padrão). Se você quiser usar um resolvedor de declarações no perfil técnico, defina isso como `true`. |
| ResolveJsonPathsInJsonTokens  | Não | Indica se o perfil técnico resolve caminhos JSON. Valores possíveis: `true`ou `false` (padrão). Use esses metadados para ler dados de um elemento JSON aninhado. Em um [OutputClaim](technicalprofiles.md#outputclaims), defina o `PartnerClaimType` para o elemento de caminho JSON que você deseja gerar. Por exemplo: `firstName.localized`ou `data.0.to.0.email`.|

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

| Atributo | Obrigatório | DESCRIÇÃO |
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

| Atributo | Obrigatório | DESCRIÇÃO |
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

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Não | O token de portador OAuth 2,0. |

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

A API REST talvez precise retornar uma mensagem de erro, como "O usuário não foi encontrado no sistema CRM". Se ocorrer um erro, a API REST deverá retornar uma mensagem de erro HTTP 409 (código de status de resposta de conflito) com os seguintes atributos:

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| version | Sim | 1.0.0 |
| status | Sim | 409 |
| código | Não | Um código de erro do provedor de ponto de extremidade RESTful, que é exibido quando `DebugMode` está habilitado. |
| requestId | Não | Um identificador de solicitação do provedor de ponto de extremidade RESTful, que é exibido quando `DebugMode` está habilitado. |
| userMessage | Sim | Uma mensagem de erro que é mostrada ao usuário. |
| developerMessage | Não | A descrição detalhada do problema e como corrigi-lo, o que é exibido quando `DebugMode` está habilitado. |
| moreInfo | Não | Um URI que aponta para informações adicionais, que são exibidas quando `DebugMode` está habilitado. |

O exemplo a seguir mostra uma API REST que retorna uma mensagem de erro formatada em JSON:

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

Consulte os seguintes artigos para obter exemplos de como usar um perfil técnico RESTful:

- [Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação da entrada do usuário](rest-api-claims-exchange-dotnet.md)
- [Proteja seus serviços RESTful usando a autenticação Básica HTTP](secure-rest-api-dotnet-basic-auth.md)
- [Proteger seu serviço RESTful usando certificados do cliente](secure-rest-api-dotnet-certificate-auth.md)
- [Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação na entrada do usuário](custom-policy-rest-api-claims-validation.md)
