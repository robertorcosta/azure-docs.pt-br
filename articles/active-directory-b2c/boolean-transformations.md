---
title: Boolean reivindica exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: Boolean reivindica exemplos de transformação para o esquema IEF (Identity Experience Framework, estrutura de experiência de identidade) do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ea3b273070702144d5296d07cb8712da044819a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471958"
---
# <a name="boolean-claims-transformations"></a>Transformações de declarações boolianas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para o uso das transformações booleanas do esquema Identity Experience Framework no Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Executa uma operação And de dois inputClaims boolianos e define o outputClaim com o resultado da operação.

| Item  | TransformationClaimType  | Tipo de Dados  | Observações |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | booleano | O primeiro ClaimType a ser avaliado. |
| InputClaim | inputClaim2  | booleano | O segundo ClaimType a ser avaliado. |
|OutputClaim | outputClaim | booleano | Os ClaimTypes que serão produzidos após a invocação dessa transformação de declarações (true ou false). |

A transformação de declarações a seguir demonstra como executar And em dois ClaimTypes boolianos: `isEmailNotExist`, e `isSocialAccount`. A declaração de saída `presentEmailSelfAsserted` será definida como `true` se o valor de ambas as declarações de entrada for `true`. Em uma etapa de orquestração, você poderá usar uma pré-condição para predefinir uma página autodeclarada, somente se o email da conta social estiver vazio.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Declarações de saída:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Verifica se os valores boolianos de duas declarações são iguais e gera uma exceção se eles não são.

| Item | TransformationClaimType  | Tipo de Dados  | Observações |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | booleano | O ClaimType a ser declarado. |
| InputParameter |valueToCompareTo | booleano | O valor a ser comparado (true ou false). |

A transformação de declarações **AssertBooleanClaimIsEqualToValue** é sempre executada por meio de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md). Os metadados do perfil técnico autodeclarado **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** controlam a mensagem de erro que o perfil técnico apresenta ao usuário. As mensagens de erro podem ser [localizadas](localization-string-ids.md#claims-transformations-error-messages).

![Execução do AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

A transformação de declarações a seguir demonstra como verificar o valor de um ClaimType booliano com um valor `true`. Se o valor de ClaimType `accountEnabled` for false, uma mensagem de erro será gerada.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


O perfil técnico de validação `login-NonInteractive` chama a transformação de declarações `AssertAccountEnabledIsTrue`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autodeclarado chama o perfil técnico **login-NonInteractive** de validação.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Resultado: erro gerado

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Verifica se o valor booleano `true` `false`de uma reivindicação é igual a ou , e retornar o resultado da compressão.

| Item | TransformationClaimType  | Tipo de Dados  | Observações |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | booleano | O ClaimType a ser declarado. |
| InputParameter |valueToCompareTo | booleano | O valor a ser comparado (true ou false). |
| OutputClaim | compararResultado | booleano | O ClaimType produzido depois de invocar esta ClaimsTransformation. |


A transformação de declarações a seguir demonstra como verificar o valor de um ClaimType booliano com um valor `true`. Se o valor `IsAgeOver21Years` do ClaimType `true`for igual `true`a , `false`a transformação de sinistros retorna, caso contrário .

```XML
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
      <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: false
- Parâmetros de entrada:
    - **valueToCompareTo**: true
- Declarações de saída:
    - **compareResultado:** falso



## <a name="notclaims"></a>NotClaims

Executa uma operação Not do inputClaim booliano e define o outputClaim com o resultado da operação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | booleano | A declaração a ser operada. |
| OutputClaim | outputClaim | booleano | Os ClaimTypes que são produzidos após a invocação desse ClaimsTransformation (true ou false). |

Use essa transformação de declaração para realizar a negação lógica em uma declaração.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: false
- Declarações de saída:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

Calcula um Or entre dois inputClaims boolianos e define o outputClaim com o resultado da operação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | booleano | O primeiro ClaimType a ser avaliado. |
| InputClaim | inputClaim2 | booleano | O segundo ClaimType a ser avaliado. |
| OutputClaim | outputClaim | booleano | Os ClaimTypes que serão produzidos após a invocação desse ClaimsTransformation (true ou false). |

A transformação de declarações a seguir demonstra como executar `Or` em dois ClaimTypes boolianos. Na etapa de orquestração, você poderá usar uma pré-condição para predefinir uma página autodeclarada se o valor de uma das declarações for `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Declarações de saída:
    - **outputClaim**: true
