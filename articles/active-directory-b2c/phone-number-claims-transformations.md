---
title: As transformações de declarações de número de telefone em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para transformações de declarações de número de telefone em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183917"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definir transformações de declarações de número de telefone no Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece referência e exemplos para usar as transformações de declarações de número de telefone do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações sobre as transformações de declarações em geral, consulte [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Converte um tipo de dados `phoneNumber` em um tipo de dados `string`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  O ClaimType para converter em uma cadeia de caracteres. |
| OutputClaim | phoneNumberString | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

Neste exemplo, a declaração cellPhoneNumber com um tipo de valor de `phoneNumber` é convertida em uma declaração de celular com um tipo de valor de `string`.

```XML
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **PhoneNumber**: + 11234567890 (PhoneNumber)
- Declarações de saída:
  - **phoneNumberString**: + 11234567890 (cadeia de caracteres)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Essa transformação de declaração valida o formato do número de telefone. Se ele estiver em um formato válido, altere-o para um formato padrão usado pelo Azure AD B2C. Se o número de telefone fornecido não estiver em um formato válido, uma mensagem de erro será retornada.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | string |  A declaração de cadeia de caracteres para o número de telefone. O número de telefone deve estar em formato internacional, completo com um "+" e código de país à esquerda. Se a declaração de entrada `country` for fornecida, o número de telefone estará no formato local (sem o código do país). |
| InputClaim | country | string | Adicional A declaração de cadeia de caracteres para o código do país do número de telefone no formato ISO3166 (o código do país ISO-3166 de duas letras). |
| OutputClaim | outputClaim | phoneNumber | O resultado dessa transformação de declarações. |

A transformação declarações **ConvertStringToPhoneNumberClaim** é sempre executada de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md) ou [controle de exibição](display-controls.md). Os metadados do perfil técnico autodeclarado **UserMessageIfClaimsTransformationInvalidPhoneNumber** controlam a mensagem de erro apresentada ao usuário.

![Diagrama do caminho de execução da mensagem de erro](./media/phone-authentication/assert-execution.png)

Você pode usar essa transformação de declarações para garantir que a declaração de cadeia de caracteres fornecida seja um número de telefone válido. Caso contrário, uma mensagem de erro será gerada. O exemplo a seguir verifica se **phoneaction** ClaimType é realmente um número de telefone válido e, em seguida, retorna o número de telefone no formato de Azure ad B2C padrão. Caso contrário, uma mensagem de erro será lançada.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

O perfil técnico autodeclarado que chama o perfil técnico de validação que contém essa transformação de declarações pode definir a mensagem de erro.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exemplo 1

- Declarações de entrada:
  - **phoneNumberString**: 033 456-7890
  - **país**: DK
- Declarações de saída:
  - **outputClaim**: + 450334567890

### <a name="example-2"></a>Exemplo 2

- Declarações de entrada:
  - **phoneNumberString**: + 1 (123) 456-7890
- Declarações de saída:
  - **outputClaim**: + 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Isso extrairá o código do país e o número nacional da declaração de entrada e, opcionalmente, lançará uma exceção se o número de telefone fornecido não for válido.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | string | A declaração de cadeia de caracteres do número de telefone. O número de telefone deve estar em formato internacional, completo com um "+" e código de país à esquerda. |
| InputParameter | throwExceptionOnFailure | booleano | Adicional Um parâmetro que indica se uma exceção é lançada quando o número de telefone não é válido. O valor padrão é falso. |
| InputParameter | countryCodeType | string | Adicional Um parâmetro que indica o tipo de código do país na declaração de saída. Os valores disponíveis são **CallingCode** (o código de chamada internacional para um país, por exemplo + 1) ou **ISO3166** (o código do país ISO-3166 de duas letras). |
| OutputClaim | nationalNumber | string | A declaração de cadeia de caracteres para o número nacional do número de telefone. |
| OutputClaim | countryCode | string | A declaração de cadeia de caracteres para o código do país do número de telefone. |


Se a transformação declarações **GetNationalNumberAndCountryCodeFromPhoneNumberString** for executada a partir de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md) ou uma [ação de controle de exibição](display-controls.md#display-control-actions), os metadados do perfil técnico **UserMessageIfPhoneNumberParseFailure** autodeclarado controlam a mensagem de erro apresentada ao usuário.

![Diagrama do caminho de execução da mensagem de erro](./media/phone-authentication/assert-execution.png)

Você pode usar essa transformação de declarações para dividir um número de telefone completo no código do país e no número nacional. Se o número de telefone fornecido não for válido, você poderá optar por lançar uma mensagem de erro.

O exemplo a seguir tenta dividir o número de telefone no número nacional e no código do país. Se o número de telefone for válido, o número de telefone será substituído pelo número nacional. Se o número de telefone não for válido, uma exceção não será lançada e o número de telefone ainda terá seu valor original.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

O perfil técnico autodeclarado que chama o perfil técnico de validação que contém essa transformação de declarações pode definir a mensagem de erro.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exemplo 1

- Declarações de entrada:
  - **PhoneNumber**: + 49 (123) 456-7890
- Parâmetros de entrada:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- Declarações de saída:
  - **nationalNumber**: 1234567890
  - **CountryCode**: de

### <a name="example-2"></a>Exemplo 2

- Declarações de entrada:
  - **PhoneNumber**: + 49 (123) 456-7890
- Parâmetros de entrada
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- Declarações de saída:
  - **nationalNumber**: 1234567890
  - **CountryCode**: + 49
