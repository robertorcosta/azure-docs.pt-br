---
title: Exemplos de transformação de declarações de cadeia de caracteres para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos de transformação de declarações de cadeia de caracteres para o esquema IEF (Identity Experience Framework) de Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 678385d9ed16a9821fc61be476e7eb9eaf6fd4f1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183696"
---
# <a name="string-claims-transformations"></a>Transformações de declarações de cadeias de caracteres

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações de cadeia de caracteres do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Compare duas declarações e lance uma exceção se não forem iguais de acordo com a comparação especificada inputClaim1, inputClaim2 e stringComparison.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo da primeira declaração, que será comparado. |
| InputClaim | inputClaim2 | string | Tipo da segunda declaração, que será comparado. |
| InputParameter | stringComparison | string | comparação da cadeia de caracteres, um dos valores: Ordinal, OrdinalIgnoreCase. |

A transformação declarações **AssertStringClaimsAreEqual** é sempre executada a partir de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md)ou um [DisplayConrtol](display-controls.md). O `UserMessageIfClaimsTransformationStringsAreNotEqual` metadados de um perfil técnico autodeclarado controla a mensagem de erro que é apresentada ao usuário.


![Execução do AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Você pode usar essa transformação de declarações para certificar-se de declarações. Dois ClaimTypes têm o mesmo valor. Caso contrário, uma mensagem de erro será gerada. O exemplo a seguir verifica se o ClaimType **strongAuthenticationEmailAddress** é igual ao ClaimType **email**. Caso contrário, uma mensagem de erro será gerada.

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


O perfil técnico de validação **login-NonInteractive** chama a transformação de declarações **AssertEmailAndStrongAuthenticationEmailAddressAreEqual**.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autodeclarado chama o perfil técnico **login-NonInteractive** de validação.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
  - **stringComparison**: ordinalIgnoreCase
- Resultado: erro gerado

## <a name="changecase"></a>ChangeCase

Altera a declaração fornecida para letra maiúscula ou minúscula, dependendo do operador.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | O ClaimType a ser alterado. |
| InputParameter | toCase | string | Um dos seguintes valores: `LOWER` ou `UPPER`. |
| OutputClaim | outputClaim | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

Use essa transformação de declaração para alterar qualquer ClaimType de cadeia de caracteres para letra maiúscula ou minúscula.

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **email**: SomeOne@contoso.com
- Parâmetros de entrada:
    - **toCase**: LOWER
- Declarações de saída:
  - **email**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Cria uma declaração de cadeia de caracteres do parâmetro de entrada fornecido na transformação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | A cadeia de caracteres a ser definida. Esse parâmetro de entrada suporta [expressões de transformação de declarações de cadeia de caracteres](string-transformations.md#string-claim-transformations-expressions). |
| OutputClaim | createdClaim | string | O ClaimType que é produzido depois de invocar esta transformação de declaração, com o valor especificado no parâmetro de entrada. |

Use essa transformação de declarações para definir um valor de ClaimType de cadeia de caracteres.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Parâmetro de entrada:
    - **value**: termos de serviço da Contoso...
- Declarações de saída:
    - **createdClaim**: O ClaimType de TOS contém o valor "Termos de serviço da Contoso...".

## <a name="compareclaims"></a>CompareClaims

Determine se uma declaração de cadeia de caracteres é igual à outra. O resultado é um novo ClaimType booliano com um valor de `true` ou `false`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo da primeira declaração, que será comparado. |
| InputClaim | inputClaim2 | string | Tipo da segunda declaração, que será comparado. |
| InputParameter | operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | ignoreCase | booleano | Especifica se essa comparação deve ignorar maiúsculas e minúsculas das cadeias de caracteres que estão sendo comparadas. |
| OutputClaim | outputClaim | booleano | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

Use essa transformação de declaração para verificar se uma declaração for igual à outra. Por exemplo, a seguinte transformação de declarações verifica se o valor da declaração **email** é igual à declaração **Verified.Email**.

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
    - **operator**: NOT EQUAL
    - **ignoreCase**: true
- Declarações de saída:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Determina se um valor de declaração é igual ao valor do parâmetro de entrada.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo da declaração, que será comparado. |
| InputParameter | operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | compareTo | string | comparação da cadeia de caracteres, um dos valores: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | booleano | Especifica se essa comparação deve ignorar maiúsculas e minúsculas das cadeias de caracteres que estão sendo comparadas. |
| OutputClaim | outputClaim | booleano | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

É possível usar essa transformação de declarações para verificar se uma declaração é igual ao valor especificado. Por exemplo, a seguinte transformação de declarações verifica se o valor da declaração **termsOfUseConsentVersion** é igual a `v1`.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo
- Declarações de entrada:
    - **inputClaim1**: v1
- Parâmetros de entrada:
    - **compareTo**: V1
    - **operator**: EQUAL
    - **ignoreCase**: true
- Declarações de saída:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Cria uma cadeia de caracteres aleatória usando o gerador de número aleatório. Se o gerador de número aleatório é do tipo `integer`, opcionalmente, um parâmetro de semente e um número máximo podem ser fornecidos. Um parâmetro de formato de cadeia de caracteres opcional permite que a saída seja formatada usando-o, e um parâmetro de base64 opcional especifica se a saída é randomGeneratorType [guid, inteiro] outputClaim (cadeia de caracteres) codificada em base64.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | Especifica o valor aleatório a ser gerado, `GUID` (ID global exclusivo) ou `INTEGER` (um número). |
| InputParameter | stringFormat | string | [Opcional] Formate o valor aleatório. |
| InputParameter | base64 | booleano | [Opcional] Converta o valor aleatório em base64. Se o formato de cadeia de caracteres for aplicado, o valor após o formato de cadeia de caracteres será codificado em base64. |
| InputParameter | maximumNumber | INT | [Opcional] Para randomGeneratorType `INTEGER` somente. Especifique o número máximo. |
| InputParameter | seed  | INT | [Opcional] Para randomGeneratorType `INTEGER` somente. Especifique a semente para valor aleatório. Observação: a mesma semente produz a mesma sequência de números aleatórios. |
| OutputClaim | outputClaim | string | Os ClaimTypes que serão produzidos depois de invocar esta transformação de declaração. O valor aleatório. |

O exemplo a seguir gera uma ID exclusiva global. Essa transformação de declarações é usada para criar o UPN (nome principal de usuário) aleatório.

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Parâmetros de entrada:
    - **randomGeneratorType**: GUID
- Declarações de saída:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

O exemplo a seguir gera um valor de inteiro aleatório entre 0 e 1.000. O valor é formatado para OTP_{valor aleatório}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Parâmetros de entrada:
    - **randomGeneratorType**: INTEGER
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **base64**: false
- Declarações de saída:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Formate uma declaração de acordo com a cadeia de caracteres de formato fornecida. Essa transformação usa o método C# `String.Format`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |string |O ClaimType que atua como o parâmetro {0} do formato da cadeia de caracteres. |
| InputParameter | stringFormat | string | O formato da cadeia de caracteres, incluindo o parâmetro {0}. Esse parâmetro de entrada suporta [expressões de transformação de declarações de cadeia de caracteres](string-transformations.md#string-claim-transformations-expressions).  |
| OutputClaim | outputClaim | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

Use essa transformação de declarações para formatar qualquer cadeia de caracteres com um parâmetro {0}. O exemplo a seguir cria um **userPrincipalName**. Todos os perfis técnicos do provedor de identidade social, como `Facebook-OAUTH` chama **CreateUserPrincipalName** para gerar um **userPrincipalName**.

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parâmetros de entrada:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Declarações de saída:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formate duas declarações de acordo com a cadeia de caracteres de formato fornecida. Essa transformação usa o método C# `String.Format`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |string | O ClaimType que atua como o parâmetro {0} do formato da cadeia de caracteres. |
| InputClaim | InputClaim | string | O ClaimType que atua como o parâmetro {1} do formato da cadeia de caracteres. |
| InputParameter | stringFormat | string | O formato da cadeia de caracteres, incluindo os parâmetros {0} e {1}. Esse parâmetro de entrada suporta [expressões de transformação de declarações de cadeia de caracteres](string-transformations.md#string-claim-transformations-expressions).   |
| OutputClaim | outputClaim | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

Use essa transformação de declarações para formatar qualquer cadeia de caracteres com dois parâmetros, {0} e {1}. O exemplo a seguir cria um **displayName** com o formato especificado:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Parâmetros de entrada:
    - **StringFormat**: {0} {1}
- Declarações de saída:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Copia cadeias de caracteres localizadas em declarações.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | O nome da cadeia de caracteres localizada | string | Lista de tipos de declaração produzidas após a chamada desta transformação de declarações. |

Para usar a transformação de declarações GetLocalizedStringsTransformation:

1. Defina uma [cadeia de caracteres de localização](localization.md) e associe-a a um [perfil técnico autodeclarado](self-asserted-technical-profile.md).
1. O `ElementType` do elemento `LocalizedString` deve ser definido como `GetLocalizedStringsTransformationClaimType`.
1. O `StringId` é um identificador exclusivo que você define e usá-lo mais tarde em sua transformação de declarações.
1. Na transformação declarações, especifique a lista de declarações a serem definidas com a cadeia de caracteres localizada. O `ClaimTypeReferenceId` é uma referência a um ClaimType já definido na seção ClaimsSchema da política. O `TransformationClaimType` é o nome da cadeia de caracteres localizada, conforme definido no `StringId` do elemento `LocalizedString`.
1. Em um [perfil técnico autodeclarado](self-asserted-technical-profile.md)ou uma transformação de declarações de entrada ou saída de [controle de exibição](display-controls.md) , faça uma referência à sua transformação de declarações.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

O exemplo a seguir pesquisa o assunto do email, o corpo, sua mensagem de código e a assinatura do email, de cadeias de caracteres localizadas. Essas declarações são usadas posteriormente pelo modelo de verificação de email personalizado.

Defina cadeias de caracteres localizadas para inglês (padrão) e espanhol.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

A transformação declarações define o valor do tipo de Declaração *sujeito* ao valor do `StringId` *email_subject*.

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de saída:
  - **assunto**: código de verificação de email da conta contoso
  - **mensagem**: Obrigado por verificar sua conta!
  - **codeIntro**: seu código é
  - **assinatura**: Atenciosamente


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Procurando um item de uma coleção de **Restrição** da declaração.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | string | A declaração que contém o texto a ser pesquisado nas declarações **restrictionValueClaim** com a coleção **Restrição**.  |
| OutputClaim | restrictionValueClaim | string | A declaração que contém a coleção **Restrição**. Depois que a transformação de declarações for invocada, o valor dessa declaração conterá o valor do item selecionado. |

O exemplo a seguir procura a descrição da mensagem de erro com base na chave de erro. A declaração **responseMsg** contém uma coleção de mensagens de erro para apresentar ao usuário final ou a ser enviada para a terceira parte confiável.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
A transformação de declarações procura o texto do item e retorna seu valor. Se a restrição é localizada usando `<LocalizedCollection>`, a transformação de declarações retorna o valor localizado.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **mapFromClaim**: B2C_V1_90001
- Declarações de saída:
    - **restrictionValueClaim**: não é possível entrar porque você é um secundário.

## <a name="lookupvalue"></a>LookupValue

Procure um valor de declaração de uma lista de valores com base no valor de outra declaração.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | A declaração que contém o valor de pesquisa |
| InputParameter | |string | Coleção de inputParameters. |
| InputParameter | errorOnFailedLookup | booleano | Controlar se um erro é retornado quando nenhuma pesquisa corresponde. |
| OutputClaim | inputParameterId | string | Os ClaimTypes que serão produzidos depois de invocar esta transformação de declaração. O valor do `Id`correspondente. |

O exemplo a seguir procura o nome de domínio em uma das coleções inputParameters. A transformação de declarações procura o nome de domínio no identificador e retorna seu valor (uma ID de aplicativo).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputParameterId**: test.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Declarações de saída:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Quando `errorOnFailedLookup` parâmetro de entrada é definido como `true`, a transformação **LookupValue** declarações é sempre executada a partir de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md)ou um [DisplayConrtol](display-controls.md). O `LookupNotFound` metadados de um perfil técnico autodeclarado controla a mensagem de erro que é apresentada ao usuário.

![Execução do AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

O exemplo a seguir procura o nome de domínio em uma das coleções inputParameters. A transformação declarações pesquisa o nome de domínio no identificador e retorna seu valor (uma ID de aplicativo) ou gera uma mensagem de erro.

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputParameterId**: Live.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Erro:
    - Nenhuma correspondência encontrada para o valor de declaração de entrada na lista de IDs de parâmetro de entrada e errorOnFailedLookup é true.


## <a name="nullclaim"></a>NullClaim

Limpe o valor de uma determinada declaração.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | O valor da declaração é definido como nulo. |

Use essa transformação de declaração para remover dados desnecessários do recipiente de propriedades de declarações, de modo que o cookie de sessão será menor. O exemplo a seguir remove o valor do tipo de declaração `TermsOfService`.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
    - **outputClaim**: bem-vindo ao aplicativo da Contoso. Se você continuar a navegar e usar esse site, você estará concordando em seguir e estará vinculado aos seguintes termos e condições...
- Declarações de saída:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Obtém a parte do domínio de um endereço de email.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | string | O ClaimType que contém o endereço de email. |
| OutputClaim | domínio | string | O ClaimType que é produzido depois de invocar esta transformação de declarações – o domínio. |

Use essa transformação de declarações para analisar o nome de domínio depois do símbolo @ do usuário. A transformação de declarações a seguir demonstra como analisar o nome de domínio de uma declaração de **email**.

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **emailAddress**: joe@outlook.com
- Declarações de saída:
    - **domain**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Verifica se uma declaração de cadeia de caracteres `claimToMatch` e `matchTo` parâmetro de entrada são iguais e define as declarações de saída com o valor presente no parâmetro de entrada `outputClaimIfMatched`, juntamente com a declaração de saída de resultado de comparação, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | string | Tipo da declaração, que será comparado. |
| InputParameter | matchTo | string | A expressão regular a ser correspondente. |
| InputParameter | outputClaimIfMatched | string | O valor a ser definido se as cadeias de caracteres forem iguais. |
| OutputClaim | outputClaim | string | Se a expressão regular for corresponder, essa declaração de saída conterá o valor de `outputClaimIfMatched` parâmetro de entrada. Ou NULL, se não houver correspondência. |
| OutputClaim | regexCompareResultClaim | booleano | A expressão regular corresponde ao tipo de declaração de saída de resultado, que deve ser definido como `true` ou `false` com base no resultado da correspondência. |

Por exemplo, verifica se o número de telefone fornecido é válido, com base no padrão de expressão regular de número de telefone.

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="setClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **claimToMatch**: "64854114520"
- Parâmetros de entrada:
    - **matchto**: "^ [0-9]{4,16}$"
    - **outputClaimIfMatched**: "isphone"
- Declarações de saída:
    - **outputClaim**: "isphone"
    - **regexCompareResultClaim**: true

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Verifica se uma declaração de cadeia de caracteres e o parâmetro de entrada `matchTo` são iguais, e define as declarações de saída com o valor presente nos parâmetros de entrada `stringMatchMsg` e `stringMatchMsgCode`, juntamente com a declaração de saída do resultado de comparação, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | Tipo da declaração, que será comparado. |
| InputParameter | matchTo | string | A cadeia de caracteres a ser comparada com `inputClaim`. |
| InputParameter | stringComparison | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | string | Primeiro valor a ser definido se as cadeias de caracteres forem iguais. |
| InputParameter | stringMatchMsgCode | string | Segundo valor a ser definido se as cadeias de caracteres forem iguais. |
| OutputClaim | outputClaim1 | string | Se as cadeias de caracteres forem iguais, essa declaração de saída conterá o valor do parâmetro de entrada `stringMatchMsg`. |
| OutputClaim | outputClaim2 | string | Se as cadeias de caracteres forem iguais, essa declaração de saída conterá o valor do parâmetro de entrada `stringMatchMsgCode`. |
| OutputClaim | stringCompareResultClaim | booleano | O tipo de declaração de saída do resultado de comparação, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

É possível usar essa transformação de declarações para verificar se uma declaração é igual ao valor especificado. Por exemplo, a seguinte transformação de declarações verifica se o valor da declaração **termsOfUseConsentVersion** é igual a `v1`. Se sim, altere o resultado para `v2`.

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: v1
- Parâmetros de entrada:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: o TOS é atualizado para a v2
- Declarações de saída:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: o TOS é atualizado para a v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Verifica se uma declaração de cadeia de caracteres e o parâmetro de entrada `matchTo` são iguais, e define as declarações de saída com o valor presente no parâmetro de entrada `outputClaimIfMatched`, juntamente com a declaração de saída do resultado de comparação, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | string | Tipo da declaração, que será comparado. |
| InputParameter | matchTo | string | A cadeia de caracteres a ser comparada com inputClaim. |
| InputParameter | stringComparison | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | string | O valor a ser definido se as cadeias de caracteres forem iguais. |
| OutputClaim | outputClaim | string | Se as cadeias de caracteres forem iguais, essa declaração de saída conterá o valor do parâmetro de entrada `outputClaimIfMatched`. Ou nulo se as cadeias de caracteres não corresponderem. |
| OutputClaim | stringCompareResultClaim | booleano | O tipo de declaração de saída do resultado de comparação, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

Por exemplo, a seguinte transformação de declarações verifica se o valor da declaração **ageGroup** é igual a `Minor`. Em caso afirmativo, retorne o valor para `B2C_V1_90001`.

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **claimToMatch**: secundário
- Parâmetros de entrada:
    - **matchTo**: secundário
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Declarações de saída:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true


## <a name="stringcontains"></a>StringContains

Determine se uma subcadeia de caracteres especificada ocorre dentro da declaração de entrada. O resultado é um novo ClaimType booliano com um valor de `true` ou `false`. `true` se o parâmetro de valor ocorrer nessa cadeia de caracteres, caso contrário, `false`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de declaração, que deve ser pesquisada. |
|InputParameter|contains|string|O valor a ser pesquisado.|
|InputParameter|ignoreCase|string|Especifica se essa comparação deve ignorar o caso da cadeia de caracteres que está sendo comparada.|
| OutputClaim | outputClaim | string | O ClaimType produzido depois de invocar esta ClaimsTransformation. Um indicador booliano se a subcadeia de caracteres ocorrer na declaração de entrada. |

Use essa transformação de declarações para verificar se um tipo de declaração de cadeia de caracteres contém uma subcadeia de caracteres. O exemplo a seguir verifica se o tipo de declaração de cadeia de caracteres `roles` contém o valor de **admin**.

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: "admin, Aprovador, editor"
- Parâmetros de entrada:
    - **contém**: "admin",
    - **ignoreCase**: true
- Declarações de saída:
    - **outputClaim**: true

## <a name="stringsubstring"></a>StringSubstring

Extrai partes de um tipo de declaração de cadeia de caracteres, começando pelo caractere na posição especificada e retorna o número especificado de caracteres.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de declaração, que contém a cadeia de caracteres. |
| InputParameter | startIndex | INT | A posição de caractere inicial com base em zero de uma subcadeia de caracteres nesta instância. |
| InputParameter | comprimento | INT | O número de caracteres na Subcadeia. |
| OutputClaim | outputClaim | booleano | Uma cadeia de caracteres equivalente à subcadeia de caracteres que começa em startIndex nessa instância, ou vazia se startIndex for igual ao comprimento dessa instância e o comprimento for zero. |

Por exemplo, obtenha o prefixo do país do número de telefone.


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: "+ 1644114520"
- Parâmetros de entrada:
    - **startIndex**: 0
    - **comprimento**: 2
- Declarações de saída:
    - **outputClaim**: "+ 1"

## <a name="stringreplace"></a>StringReplace

Pesquisa uma cadeia de caracteres de tipo de declaração em um valor especificado e retorna uma nova cadeia de caracteres de tipo de declaração na qual todas as ocorrências de uma cadeia de caracteres especificada na cadeia de caracteres atual são substituídas por outra cadeia de caracteres especificada.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de declaração, que contém a cadeia de caracteres. |
| InputParameter | oldValue | string | A cadeia de caracteres a ser pesquisada. |
| InputParameter | newValue | string | A cadeia de caracteres para substituir todas as ocorrências de `oldValue` |
| OutputClaim | outputClaim | booleano | Uma cadeia de caracteres equivalente à cadeia de caracteres atual, exceto que todas as instâncias de oldValue são substituídas por newValue. Se oldValue não for encontrado na instância atual, o método retornará a instância atual inalterada. |

Por exemplo, Normalize um número de telefone removendo os caracteres `-`


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: "+ 164-411-452-054"
- Parâmetros de entrada:
    - **OldValue**: "-"
    - **comprimento**: ""
- Declarações de saída:
    - **outputClaim**: "+ 164411452054"

## <a name="stringjoin"></a>StringJoin

Concatena os elementos de um tipo de declaração de coleção de cadeia de caracteres especificado, usando o separador especificado entre cada elemento ou membro.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | Uma coleção que contém as cadeias de caracteres a serem concatenadas. |
| InputParameter | delimiter | string | A cadeia de caracteres a ser usada como separador, como `,`por vírgula. |
| OutputClaim | outputClaim | string | Uma cadeia de caracteres que consiste nos membros da coleção de cadeia de caracteres `inputClaim`, delimitada pelo parâmetro de entrada `delimiter`. |

O exemplo a seguir usa uma coleção de cadeias de caracteres de funções de usuário e a converte em uma cadeia de caracteres delimitadora de vírgula. Você pode usar esse método para armazenar uma coleção de cadeia de caracteres na conta de usuário do Azure AD. Posteriormente, quando você ler a conta do diretório, use a `StringSplit` para converter a cadeia de caracteres delimitadores de vírgula para a coleção de cadeias de caracteres.

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim**: ["admin", "autor", "leitor"]
- Parâmetros de entrada:
  - **delimitador**: ","
- Declarações de saída:
  - **outputClaim**: "administrador, autor, leitor"


## <a name="stringsplit"></a>StringSplit

Retorna uma matriz de cadeia de caracteres que contém as subcadeias de caracteres desta instância que são delimitadas por elementos de uma cadeia de caracteres especificada.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | Um tipo de declaração String que contém as subcadeias de caracteres a serem divididas. |
| InputParameter | delimiter | string | A cadeia de caracteres a ser usada como separador, como `,`por vírgula. |
| OutputClaim | outputClaim | stringCollection | Uma coleção de cadeias de caracteres cujos elementos contêm as subcadeias nesta cadeia de caracteres delimitadas pelo parâmetro de entrada `delimiter`. |

O exemplo a seguir usa uma cadeia de caracteres delimitadores de vírgula de funções de usuário e a converte em uma coleção de cadeias de caracteres.

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim**: "administrador, autor, leitor"
- Parâmetros de entrada:
  - **delimitador**: ","
- Declarações de saída:
  - **outputClaim**: ["admin", "autor", "leitor"]

## <a name="string-claim-transformations-expressions"></a>Expressões de transformações de declaração de cadeia de caracteres
Expressões de transformações de declaração em Azure AD B2C políticas personalizadas fornecem informações de contexto sobre a ID do locatário e a ID do perfil técnico.

  | Expression | DESCRIÇÃO | Exemplo |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | O nome de ProfileId técnico. | Facebook – OAUTH |
 | `{RelyingPartyTenantId}` | A ID do locatário da política de terceira parte confiável. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | A ID do locatário e da estrutura de confiança. | your-tenant.onmicrosoft.com |
