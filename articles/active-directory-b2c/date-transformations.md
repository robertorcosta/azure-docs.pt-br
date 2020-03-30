---
title: Exemplos de transformação de reivindicações de data para políticas personalizadas
description: Exemplos de transformação de reivindicações de data para o esquema IEF (Identity Experience Framework, estrutura de experiência de identidade) do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c02ac9392d6f3f95deef38ff86250e96dfb76d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476681"
---
# <a name="date-claims-transformations"></a>Transformações de declarações de data

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para o uso das transformações de reivindicações de data do esquema Identity Experience Framework no Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Verifica se uma declaração de data e hora (tipo de dados de cadeia de caracteres) é posterior a uma segunda declaração de data e hora (tipo de dados de cadeia de caracteres) e gera uma exceção.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | string | O tipo da primeira declaração, que deve ser posterior à segunda declaração. |
| InputClaim | rightOperand | string | O tipo da segunda declaração, que deve ser anterior à primeira declaração. |
| InputParameter | AssertIfEqualTo | booleano | Especifica se essa declaração deve passar se o operando esquerdo for igual ao operando direito. |
| InputParameter | AssertIfRightOperandIsNotPresent | booleano | Especifica se essa declaração deve passar se o operando à direita estiver ausente. |
| InputParameter | TreatAsEqualIfWithinMillseconds | INT | Especifica o número de milissegundos para permitir entre as duas datas e horas para considerar os tempos de igual (por exemplo, a conta para defasagem horária). |

A transformação de declarações **AssertDateTimeIsGreaterThan** é sempre executada a partir um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md). Os metadados **DateTimeGreaterThan** do perfil técnico autodeclarado controla a mensagem de erro que o perfil técnico apresenta ao usuário. As mensagens de erro podem ser [localizadas](localization-string-ids.md#claims-transformations-error-messages).

![Execução do AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

O exemplo a seguir compara as `currentDateTime` declarações à declaração `approvedDateTime`. Um erro será gerado se `currentDateTime` for posterior a `approvedDateTime`. A transformação trata valores como iguais se eles estiverem dentro de diferença de (30000 milissegundos) de 5 minutos.

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

O perfil técnico de validação `login-NonInteractive` chama a transformação de declarações `AssertApprovedDateTimeLaterThanCurrentDateTime`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autodeclarado chama o perfil técnico **login-NonInteractive** de validação.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **leftOperand**: 2020-03-01T15:00:00.0000000Z
    - **rightOperand**: 2020-03-01T14:00:00.0000000Z
- Resultado: erro gerado

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Converte uma **Data** ClaimType para um **DateTime** ClaimType. A transformação de declarações converte o formato de hora e adiciona 12:00:00 AM à data.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | date | O ClaimType a ser convertido. |
| OutputClaim | outputClaim | dateTime | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

O exemplo a seguir demonstra a conversão da declaração `dateOfBirth` (tipo de dados de data) para uma outra declaração `dateOfBirthWithTime` (tipo de dados de data e hora).

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: 2020-15-03
- Declarações de saída:
    - **outputClaim**: 2020-15-03T00:00:00.0000000Z

## <a name="convertdatetimetodateclaim"></a>converteto-datade-data

Converte um Tipo de Reclamação **de Data-Hora** em um Tipo de Reclamação **de Data.** A transformação de sinistros remove o formato de tempo da data.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | dateTime | O ClaimType a ser convertido. |
| OutputClaim | outputClaim | date | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

O exemplo a seguir demonstra `systemDateTime` a conversão do pedido `systemDate` (tipo de dados dateTime) para outro pedido (tipo de dados de data).

```XML
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim**: 2020-15-03T11:34:22.000000Z
- Declarações de saída:
  - **outputClaim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Obtenha a data UTC atual e a hora e adicione o valor para um ClaimType.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

* Declarações de saída:
    * **date:** 2020-15-03T11:40:35.000000Z

## <a name="datetimecomparison"></a>DateTimeComparison

Determine se uma dateTime é posterior, anterior ou igual a outra. O resultado é um novo ClaimType booliano com um valor de `true` ou `false`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | O primeiro dateTime a fim de comparar se é anterior ou posterior ao segundo dateTime. Valor nulo gerará uma exceção. |
| InputClaim | secondDateTime | dateTime | O segundo dateTime a fim de comparar se é anterior ou posterior ao primeiro dateTime. Valor nulo é tratado como o dateTime atual. |
| InputParameter | operador | string | Um dos seguintes valores: mesmo, posterior ou anterior. |
| InputParameter | timeSpanInSeconds | INT | Adicione o intervalo de tempo para a primeira data e hora. |
| OutputClaim | result | booleano | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

Use essa transformação de declarações para determinar se duas ClaimTypes são iguais, posteriores ou anteriores em comparação com as outras. Por exemplo, você pode armazenar a última vez em que um usuário aceitou os termos de serviço (TOS). Depois de três meses, você pode pedir ao usuário para acessar o TOS novamente.
Para executar a transformação de declaração, primeiro você precisa obter a data e hora atuais e também o último usuário de tempo aceita os termos de serviço.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **firstDateTime**: 2020-01-01T00:00:00.100000Z
    - **segundoData:** 2020-04-01T00:00:00.100000Z
- Parâmetros de entrada:
    - **operator**: posterior a
    - **timeSpanInSeconds**: 7776000 (90 dias)
- Declarações de saída:
    - **resultado**: verdadeiro
