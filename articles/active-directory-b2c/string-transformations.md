---
title: Exemplos de transformação de reivindicações de strings para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos de transformação de reivindicações de strings para o esquema IEF (Identity Experience Framework, estrutura de experiência de identidade) do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: acacba591c9b895f1bd6abfbab5d3d4a4c858d12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472768"
---
# <a name="string-claims-transformations"></a>Transformações de declarações de cadeias de caracteres

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para o uso das transformações de reivindicações de string do esquema Identity Experience Framework no Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Compare duas declarações e lance uma exceção se não forem iguais de acordo com a comparação especificada inputClaim1, inputClaim2 e stringComparison.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo da primeira declaração, que será comparado. |
| InputClaim | inputClaim2 | string | Tipo da segunda declaração, que será comparado. |
| InputParameter | stringComparison | string | comparação da cadeia de caracteres, um dos valores: Ordinal, OrdinalIgnoreCase. |

A transformação de reivindicações **AssertStringClaimsAreEqual** é sempre executada a partir de um [perfil técnico](validation-technical-profile.md) de validação que é chamado por um perfil [técnico auto-afirmado](self-asserted-technical-profile.md), ou um [DisplayConrtol](display-controls.md). Os `UserMessageIfClaimsTransformationStringsAreNotEqual` metadados de um perfil técnico auto-afirmado controlam a mensagem de erro apresentada ao usuário. As mensagens de erro podem ser [localizadas](localization-string-ids.md#claims-transformations-error-messages).


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
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Parâmetros de entrada:
  - **stringComparison**: ordinalIgnoreCase
- Resultado: erro gerado

## <a name="changecase"></a>ChangeCase

Altera a declaração fornecida para letra maiúscula ou minúscula, dependendo do operador.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | O Tipo de Reclamação a ser alterado. |
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
  - **e-mail**:SomeOne@contoso.com
- Parâmetros de entrada:
    - **toCase**: LOWER
- Declarações de saída:
  - **e-mail**:someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Cria uma reivindicação de string a partir do parâmetro de entrada fornecido na transformação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | A corda a ser definida. Este parâmetro de entrada suporta [expressões de transformação de reivindicações de string .](string-transformations.md#string-claim-transformations-expressions) |
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
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Parâmetros de entrada:
    - **operator**: NOT EQUAL
    - **ignorarCase:** verdadeiro
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
| InputParameter | stringFormat | string | O formato da cadeia de caracteres, incluindo o parâmetro {0}. Este parâmetro de entrada suporta [expressões de transformação de reivindicações de string .](string-transformations.md#string-claim-transformations-expressions)  |
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
  - **saídaReclamação:**cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formate duas declarações de acordo com a cadeia de caracteres de formato fornecida. Essa transformação usa o método C# `String.Format`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |string | O ClaimType que atua como o parâmetro {0} do formato da cadeia de caracteres. |
| InputClaim | InputClaim | string | O ClaimType que atua como o parâmetro {1} do formato da cadeia de caracteres. |
| InputParameter | stringFormat | string | O formato da cadeia de caracteres, incluindo os parâmetros {0} e {1}. Este parâmetro de entrada suporta [expressões de transformação de reivindicações de string .](string-transformations.md#string-claim-transformations-expressions)   |
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
    - **stringFormat** {0} :{1}
- Declarações de saída:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Copia strings localizadas em reivindicações.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | O nome da seqüência localizada | string | Lista de tipos de sinistros que são produzidos após essa transformação de sinistros foi invocado. |

Para usar a transformação de reivindicações GetLocalizedStringsTransformation:

1. Defina uma [seqüência de localização](localization.md) e associe-a a um [perfil técnico-auto-afirmado](self-asserted-technical-profile.md).
1. O `ElementType` do `LocalizedString` elemento deve `GetLocalizedStringsTransformationClaimType`ser definido como .
1. O `StringId` é um identificador único que você define e o usa mais tarde na transformação de suas reivindicações.
1. Na transformação de sinistros, especifique a lista de reivindicações a serem definidas com a seqüência localizada. É `ClaimTypeReferenceId` uma referência a um ClaimType já definido na seção ClaimsSchema na diretiva. O `TransformationClaimType` é o nome da seqüência `StringId` localizada `LocalizedString` como definido no elemento.
1. Em um [perfil técnico auto-afirmado,](self-asserted-technical-profile.md)ou uma mudança de entrada ou saída de sinistros de [controle de exibição,](display-controls.md) faça uma referência à transformação de suas reivindicações.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

O exemplo a seguir analisa o assunto de e-mail, o corpo, sua mensagem de código e a assinatura do e-mail, a partir de strings localizadas. Essas reivindicações posteriormente usadas pelo modelo de verificação de e-mail personalizado.

Defina strings localizadas para inglês (padrão) e espanhol.

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

A transformação de sinistros *subject* define o valor `StringId` do sujeito do tipo de sinistro com o valor do *email_subject*.

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
  - **assunto**: Código de verificação de e-mail da conta contoso
  - **mensagem**: Obrigado por verificar sua conta!
  - **codeIntro**: Seu código é
  - **assinatura**: Sinceramente


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
    - **restriçãoValueClaim**: Você não pode fazer login porque você é menor.

## <a name="lookupvalue"></a>LookupValue

Procure um valor de declaração de uma lista de valores com base no valor de outra declaração.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | A declaração que contém o valor de pesquisa |
| InputParameter | |string | Coleção de inputParameters. |
| InputParameter | errorOnFailedLookup | booleano | Controlar se um erro é retornado quando nenhuma pesquisa corresponde. |
| OutputClaim | inputParameterId | string | Os ClaimTypes que serão produzidos depois de invocar esta transformação de declaração. O valor da `Id`correspondência. |

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

Quando `errorOnFailedLookup` o parâmetro de `true`entrada é definido para , a transformação de reivindicações **Do LookupValue** é sempre executada a partir de um [perfil técnico](validation-technical-profile.md) de validação que é chamado por um perfil técnico [auto-afirmado](self-asserted-technical-profile.md), ou um [DisplayConrtol](display-controls.md). Os `LookupNotFound` metadados de um perfil técnico auto-afirmado controlam a mensagem de erro apresentada ao usuário.

![Execução do AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

O exemplo a seguir procura o nome de domínio em uma das coleções inputParameters. A transformação de sinistros pesquisa o nome de domínio no identificador e retorna seu valor (um ID de aplicativo) ou levanta uma mensagem de erro.

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
    - **parâmetro de entradaId:** live.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **erroOnFailedLookup**: true
- Erro:
    - Nenhuma correspondência encontrada para o valor da reivindicação de entrada na lista de ids de parâmetro de entrada e erroOnFailedLookup é verdadeira.


## <a name="nullclaim"></a>NullClaim

Limpe o valor de uma determinada declaração.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | O valor da reivindicação é definido como NULO. |

Use essa transformação de sinistro para remover dados desnecessários do saco de propriedade de sinistros para que o cookie da sessão seja menor. O exemplo a seguir remove o valor do tipo de declaração `TermsOfService`.

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
  - **endereço de e-mail**:joe@outlook.com
- Declarações de saída:
    - **domain**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Verifica se uma `claimToMatch` `matchTo` reivindicação de string e parâmetro de entrada são `outputClaimIfMatched` iguais, e define as reivindicações de saída com `true` `false` o valor presente no parâmetro de entrada, juntamente com a reivindicação de saída de resultado de comparação, que deve ser definida como ou com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | string | Tipo da declaração, que será comparado. |
| InputParameter | matchTo | string | A expressão regular de correspondência. |
| InputParameter | outputClaimIfMatched | string | O valor a ser definido se as cadeias de caracteres forem iguais. |
| OutputClaim | outputClaim | string | Se a expressão regular for compatível, `outputClaimIfMatched` esta reivindicação de saída contém o valor do parâmetro de entrada. Ou nulo, se não for páreo. |
| OutputClaim | regexCompareResultadoClaimClaim | booleano | O tipo de reclamação de saída de resultado `true` `false` de expressão regular, que deve ser definido como ou com base no resultado da correspondência. |

Por exemplo, verifica se o número de telefone fornecido é válido, com base no padrão de expressão regular do número de telefone.

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
    - **reivindicaçãoToMatch**: "64854114520"
- Parâmetros de entrada:
    - **matchTo**: "^[0-9]{4,16}$"
    - **saídaClaimIfMatched**: "isPhone"
- Declarações de saída:
    - **saídaReclamação:**"isPhone"
    - **regexCompareResultClaimClaim**: verdadeiro

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
    - **stringComparação:** ordinalIgnoreCase
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
    - **stringComparação:** ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Declarações de saída:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true


## <a name="stringcontains"></a>StringContains

Determine se uma substring especificada ocorre dentro da reivindicação de entrada. O resultado é um novo ClaimType booliano com um valor de `true` ou `false`. `true`se o parâmetro de valor ocorrer dentro `false`desta string, caso contrário, .

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de sinistro, que deve ser pesquisado. |
|InputParameter|contains|string|O valor para pesquisar.|
|InputParameter|ignoreCase|string|Especifica se essa comparação deve ignorar o caso da seqüência de caracteres a ser comparada.|
| OutputClaim | outputClaim | string | O ClaimType produzido depois de invocar esta ClaimsTransformation. Um indicador booleano se a substring ocorrer dentro da reivindicação de entrada. |

Use esta transformação de reivindicações para verificar se um tipo de reclamação de string contém uma substring. Exemplo a seguir, `roles` verifica se o tipo de reclamação de string contém o valor do **admin**.

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
    - **inputClaim**: "Admin, Approver, Editor"
- Parâmetros de entrada:
    - **contém**: "admin",
    - **ignorarCase:** verdadeiro
- Declarações de saída:
    - **outputClaim**: true

## <a name="stringsubstring"></a>StringSubstring

Extrai partes de um tipo de reivindicação de seqüência, começando pelo caractere na posição especificada, e retorna o número especificado de caracteres.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de reclamação, que contém a seqüência. |
| InputParameter | startIndex | INT | A posição de caractere de início de base zero de uma subcadeia de caracteres nesta instância. |
| InputParameter | comprimento | INT | O número de caracteres na subcadeia de caracteres. |
| OutputClaim | outputClaim | booleano | Uma string equivalente à substring de comprimento que começa no startIndex nesta instância, ou Empty if startIndex é igual ao comprimento desta instância e o comprimento é zero. |

Por exemplo, obtenha o prefixo de número de telefone do país.


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
    - **inputClaim**: "+1644114520"
- Parâmetros de entrada:
    - **startIndex**: 0
    - **comprimento:** 2
- Declarações de saída:
    - **saídaReclamação**: "+1"

## <a name="stringreplace"></a>StringReplace

Pesquisa uma seqüência de tipo de solicitação para um valor especificado e retorna uma nova seqüência de tipo de solicitação na qual todas as ocorrências de uma seqüência de string especificada na seqüência atual são substituídas por outra seqüência especificada.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de reclamação, que contém a seqüência. |
| InputParameter | oldValue | string | A corda a ser revistada. |
| InputParameter | newValue | string | A seqüência para substituir todas as ocorrências de`oldValue` |
| OutputClaim | outputClaim | booleano | Uma string equivalente à seqüência atual, exceto que todas as instâncias do oldValue são substituídas pelo newValue. Se o oldValue não for encontrado na instância atual, o método reaverá a instância atual inalterada. |

Por exemplo, normalize um número de `-` telefone, removendo os caracteres


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
    - **inputClaim**: "+164-411-452-054"
- Parâmetros de entrada:
    - **oldValue**: "-"
    - **comprimento:**""
- Declarações de saída:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin

Concatena os elementos de um tipo de reivindicação de coleção de stringespecificado, usando o separador especificado entre cada elemento ou membro.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | Uma coleção que contém as cadeias de caracteres a serem concatenadas. |
| InputParameter | delimiter | string | A corda para usar como separador, `,`como a comma. |
| OutputClaim | outputClaim | string | Uma seqüência que consiste `inputClaim` nos membros da coleção `delimiter` de cordas, delimitada pelo parâmetro de entrada. |

O exemplo a seguir pega uma coleção de strings de funções do usuário e converte-a em uma seqüência de delimitador de comma. Você pode usar este método para armazenar uma coleção de strings na conta de usuário do Azure AD. Mais tarde, quando você ler a conta `StringSplit` do diretório, use o para converter a string delimitador de comma de volta à coleção de strings.

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
  - **inputClaim**: [ "Admin", "Autor", "Reader" ]
- Parâmetros de entrada:
  - **delimitador**: ","
- Declarações de saída:
  - **outputClaim**: "Admin,Author,Reader"


## <a name="stringsplit"></a>StringSplit

Retorna uma matriz de strings que contém as substrings nesta instância que são delimitadas por elementos de uma seqüência de stringespecificada.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | Um tipo de reivindicação de strings que contém as sub strings para dividir. |
| InputParameter | delimiter | string | A corda para usar como separador, `,`como a comma. |
| OutputClaim | outputClaim | stringCollection | Uma coleção de cordas cujos elementos contêm as substrings nesta seqüência que são delimitadas pelo parâmetro `delimiter` de entrada. |

O exemplo a seguir pega uma seqüência de delimitador de comma de funções do usuário e converte-a em uma coleção de strings.

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
  - **inputClaim**: "Admin,Author,Reader"
- Parâmetros de entrada:
  - **delimitador**: ","
- Declarações de saída:
  - **outputClaim**: [ "Admin", "Autor", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>String claim transformações expressões
As expressões de transformação de sinistros nas políticas personalizadas azure AD B2C fornecem informações de contexto sobre o ID do inquilino e o ID do perfil técnico.

  | Expression | Descrição | Exemplo |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | O perfil técnico id nome. | OAUTH do Facebook |
 | `{RelyingPartyTenantId}` | A ID do locatário da política de terceira parte confiável. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | A ID do locatário e da estrutura de confiança. | your-tenant.onmicrosoft.com |
