---
title: O número de telefone reivindica transformações em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para transformações de reivindicações de número de telefone no Azure AD B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183917"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definir transformações de reivindicações de número de telefone no Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece referência e exemplos para o uso das transformações de reivindicações de número de telefone do esquema Identity Experience Framework no Azure Active Directory B2C (Azure AD B2C). Para obter mais informações sobre transformações de sinistros em geral, consulte [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimtostring

Converte `phoneNumber` um tipo `string` de dados em um tipo de dados.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  O ClaimType para converter em uma cadeia de caracteres. |
| OutputClaim | telefoneNumberString | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

Neste exemplo, a reivindicação do celularNumber `phoneNumber` com um tipo de valor é convertida `string`em uma reivindicação de celular com um tipo de valor de .

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
  - **telefone :**+11234567890 (telefone)
- Declarações de saída:
  - **telefoneNumberString**: +11234567890 (string)


## <a name="convertstringtophonenumberclaim"></a>ConvertstringtoPhoneNumberclaim

Essa transformação de sinistro valida o formato do número de telefone. Se estiver em um formato válido, altere-o para um formato padrão usado pelo Azure AD B2C. Se o número de telefone fornecido não estiver em um formato válido, uma mensagem de erro será devolvida.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | telefoneNumberString | string |  A reclamação de seqüência para o número de telefone. O número de telefone tem que ser em formato internacional, completo com um "+" líder e código de país. Se a `country` solicitação de entrada for fornecida, o número de telefone será em formato local (sem o código do país). |
| InputClaim | country | string | [Opcional] A reivindicação de string para o código de país do número de telefone no formato ISO3166 (o código de país ISO-3166 de duas letras). |
| OutputClaim | outputClaim | phoneNumber | O resultado dessa transformação de reivindicações. |

A transformação de reivindicações **ConvertStringToPhoneNumberClaim** é sempre executada a partir de um [perfil técnico](validation-technical-profile.md) de validação que é chamado por um [perfil técnico auto-afirmado](self-asserted-technical-profile.md) ou [controle de exibição](display-controls.md). O **usuárioMessageIfClaimsTransformationTransformationInvalidPhoneNumber** auto-afirmado metadados de perfil técnico controla a mensagem de erro que é apresentada ao usuário.

![Diagrama do caminho de execução da mensagem de erro](./media/phone-authentication/assert-execution.png)

Você pode usar essa transformação de reivindicações para garantir que a reclamação de string fornecida seja um número de telefone válido. Caso contrário, uma mensagem de erro será gerada. O exemplo a seguir verifica se o **telefoneString** ClaimType é de fato um número de telefone válido e, em seguida, retorna o número de telefone no formato Padrão Azure AD B2C. Caso contrário, uma mensagem de erro é lançada.

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

O perfil técnico auto-afirmado que chama o perfil técnico de validação que contém essa transformação de sinistros pode definir a mensagem de erro.

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
  - **telefoneNumberString**: 033 456-7890
  - **país**: DK
- Declarações de saída:
  - **outputClaim**: +45034567890

### <a name="example-2"></a>Exemplo 2

- Declarações de entrada:
  - **telefoneNumberString**: +1 (123) 456-7890
- Declarações de saída:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberandCountryCodeFromPhoneNumberstring

Isso extrai o código do país e o número nacional da reivindicação de entrada, e, opcionalmente, abre uma exceção se o número de telefone fornecido não for válido.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | string | A reclamação de seqüência do número de telefone. O número de telefone tem que ser em formato internacional, completo com um "+" líder e código de país. |
| InputParameter | throwExceptionOnFailure | booleano | [Opcional] Um parâmetro indicando se uma exceção é lançada quando o número de telefone não é válido. O valor padrão é falso. |
| InputParameter | countryCodeType | string | [Opcional] Um parâmetro indicando o tipo de código de país na reivindicação de saída. Os valores disponíveis são **CallingCode** (o código de chamada internacional para um país, por exemplo +1) ou **ISO3166** (o código de país ISO-3166 de duas letras). |
| OutputClaim | número nacional | string | A reivindicação de string para o número nacional do número de telefone. |
| OutputClaim | countryCode | string | A reivindicação de string para o código do país do número de telefone. |


Se a transformação de reivindicações **GetNationalNumberAndCountryCodeFromPhoneNumberString** for executada a partir de um [perfil técnico](validation-technical-profile.md) de validação que é chamado por um perfil [técnico auto-afirmado](self-asserted-technical-profile.md) ou por uma ação de controle de [exibição,](display-controls.md#display-control-actions)então o **usuárioMessageIfPhoneNumberParseFailure** auto-afirmado metadados de perfil técnico controla a mensagem de erro que é apresentada ao usuário.

![Diagrama do caminho de execução da mensagem de erro](./media/phone-authentication/assert-execution.png)

Você pode usar essa transformação de sinistros para dividir um número de telefone completo no código do país e no número nacional. Se o número de telefone fornecido não for válido, você pode optar por enviar uma mensagem de erro.

O exemplo a seguir tenta dividir o número de telefone em número nacional e código de país. Se o número de telefone for válido, o número de telefone será substituído pelo número nacional. Se o número de telefone não for válido, uma exceção não será lançada e o número de telefone ainda tem seu valor original.

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

O perfil técnico auto-afirmado que chama o perfil técnico de validação que contém essa transformação de sinistros pode definir a mensagem de erro.

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
  - **telefone :**+49 (123) 456-7890
- Parâmetros de entrada:
  - **throwExceptionOnFailure:** false
  - **countryCodeType**: ISO3166
- Declarações de saída:
  - **número nacional**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>Exemplo 2

- Declarações de entrada:
  - **telefone :**+49 (123) 456-7890
- Parâmetros de entrada
  - **throwExceptionOnFailure:** false
  - **countryCodeType**: CallingCode
- Declarações de saída:
  - **número nacional**: 1234567890
  - **countryCode**: +49
