---
title: JSON reivindica exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: A JSON reivindica exemplos de transformação para o esquema IEF (Identity Experience Framework, estrutura de experiência de identidade) do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b42c2a414333e7ed262441321a808fc45425fc3b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756747"
---
# <a name="json-claims-transformations"></a>Transformações de declarações JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para o uso das transformações de reivindicações JSON do esquema Identity Experience Framework no Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>Gerar Json

Use valores de reivindicação ou constantes para gerar uma seqüência json. A seqüência de caminho após a notação de ponto é usada para indicar onde inserir os dados em uma seqüência JSON. Após a divisão por dosts, quaisquer inteiros são interpretados como o índice de uma matriz JSON e os não-inteiros são interpretados como o índice de um objeto JSON.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Qualquer notação de nota de nota de seqüência seguindo | string | O JsonPath do JSON onde o valor da reivindicação será inserido. |
| InputParameter | Qualquer notação de nota de nota de seqüência seguindo | string | O JsonPath do JSON onde o valor constante da seqüência será inserido. |
| OutputClaim | outputClaim | string | A seqüência JSON gerada. |

O exemplo a seguir gera uma seqüência JSON baseada no valor de reclamação de "email" e "otp", bem como strings constantes.

```XML
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

As seguintes reivindicações de transformação de saídas uma reivindicação de seqüência JSON que será o corpo da solicitação enviada ao SendGrid (um provedor de e-mail de terceiros). A estrutura do objeto JSON é definida pelos IDs na notação de nota dos Parâmetros de Entrada e dos Tipos de Declaração de Transformação das Alegações de Entrada. Os números na notação de dot implicam matrizes. Os valores vêm dos valores do InputClaims e das propriedades "Valor" dos InputParameters.

- Reivindicações de entrada:
  - **e-mail**, personalizações do tipo de solicitaçãosomeone@example.comde **transformação.0.to.0.email**: " "
  - **otp**, personalizações do tipo de reivindicação **de transformação.0.dynamic_template_data.otp** "346349"
- Parâmetro de entrada:
  - **template_id**: "d-4c56ffb40fa648b1aa682283df94f60"
  - **de.email:**service@contoso.com"
  - **personalizações.0.subject** "Código de verificação de e-mail da conta Contoso"
- Declaração de saída:
  - **requestBody**: valor JSON

```JSON
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Obter um elemento especificado de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são usados pela transformação de declarações para obter o item. |
| InputParameter | claimToExtract | string | o nome do elemento JSON a ser extraído. |
| OutputClaim | extractedClaim | string | O ClaimType que é produzido após a invocação dessa transformação de declarações, o valor do elemento especificado no parâmetro de entrada _claimToExtract_. |

No exemplo a seguir, a transformação de declarações extraiu o elemento `emailAddress` dos dados JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Parâmetro de entrada:
    - **claimToExtract**: emailAddress
- Declarações de saída:
  - **extractedClaim**:someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obter uma lista de elementos especificados de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | Os ClaimTypes que são usados pela transformação de declarações para obter as declarações. |
| InputParameter | errorOnMissingClaims | booleano | Especifica se um erro deverá ser gerado se uma das declarações estiver ausente. |
| InputParameter | includeEmptyClaims | string | Especifica se você deseja incluir declarações vazias. |
| InputParameter | jsonSourceKeyName | string | Nome da chave do elemento |
| InputParameter | jsonSourceValueName | string | Nome do valor do elemento |
| OutputClaim | Coleção | string, int, boolean e datetime |Lista de declarações a serem extraídas. O nome da declaração deve ser igual ao especificado na declaração de entrada _jsonSourceClaim_. |

No exemplo a seguir, a transformação de declarações extrai as declarações a seguir: email (string), displayName (string), membershipNum (int), active (boolean) e birthdate (datetime) dos dados JSON.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- Parâmetros de entrada:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Declarações de saída:
  - **e-mail**: "someone@example.com"
  - **displayName**: "Someone"
  - **membershipNum**: 6353399
  - **active**: true
  - **birthdate**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Obtém um elemento (longo) numérico especificado dos dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são usados pela transformação de declarações para obter a declaração. |
| InputParameter | claimToExtract | string | O nome do elemento JSON a ser extraído. |
| OutputClaim | extractedClaim | long | O ClaimType que é produzido após a invocação desse ClaimsTransformation, o valor do elemento especificado no parâmetro de entrada _claimToExtract_. |

No exemplo a seguir, a transformação de declarações extrai o elemento `id` dos dados JSON.

```JSON
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id": 6353399}
- Parâmetros de entrada
    - **claimToExtract**: id
- Declarações de saída:
    - **extractedClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Obtém o primeiro elemento a partir de um dado JSON.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os Tipos de Sinistros que são usados pela transformação de sinistros para obter o item a partir dos dados JSON. |
| OutputClaim | chave | string | A chave do primeiro elemento no JSON. |
| OutputClaim | value | string | O primeiro valor de elemento no JSON. |

No exemplo a seguir, a transformação de sinistros extrai o primeiro elemento (nome) dos dados JSON.

```XML
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputJson**: {"givenName": "Emilty", "lastName": "Smith"}
- Declarações de saída:
  - **chave**: dadoNome
  - **valor**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtém o primeiro elemento de uma matriz de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | Os ClaimTypes que são usados pela transformação de declarações para obter o item da matriz JSON. |
| OutputClaim | extractedClaim | string | O ClaimType que é produzido após a invocação desse ClaimsTransformation, o primeiro elemento na matriz JSON. |

No exemplo a seguir, a transformação de declarações extrai o primeiro elemento (endereço de email) da matriz JSON `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **entradaJsonClaim**:someone@example.com[" ", "Alguém", 6353399]
- Declarações de saída:
  - **extractedClaim**:someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Converte dados XML no formato JSON.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | string | Os ClaimTypes que são usados pela transformação de declarações para converter os dados do formato XML para JSON. |
| OutputClaim | json | string | O ClaimType que é produzido após a invocação dessee ClaimsTransformation, os dados no formato JSON. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

No exemplo a seguir, a transformação de declarações converte os dados XML a seguir no formato JSON.

#### <a name="example"></a>Exemplo
Declaração de entrada:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Declaração de saída:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


