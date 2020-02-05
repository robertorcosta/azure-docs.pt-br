---
title: Exemplos de transformação de declarações gerais para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos gerais de transformação de declarações para o esquema IEF (Identity Experience Framework) de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98d9730168764f0ba683a246f9ac224c13d3bf31
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982799"
---
# <a name="general-claims-transformations"></a>Transformações de declarações gerais

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações gerais do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>CopyClaim

Copiar o valor de uma declaração para outra. Ambas as declarações devem ser do mesmo tipo.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | Cadeia de caracteres, int | O tipo de declaração que deve ser copiada. |
| OutputClaim | outputClaim | Cadeia de caracteres, int | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

Use essa transformação de declarações para copiar um valor de uma declaração de cadeia de caracteres ou numérica para outra declaração. O exemplo a seguir copia o valor de declaração externalEmail para a declaração de email.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim"> 
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: bob@contoso.com
- Declarações de saída:
    - **outputClaim**: bob@contoso.com 

## <a name="doesclaimexist"></a>DoesClaimExist

Verifica se a **inputClaim** existe ou não e define **outputClaim** como true ou false adequadamente.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Qualquer | A declaração de entrada cuja existência deve ser verificada. |
| OutputClaim | outputClaim | booleano | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

Use essa transformação de declarações para verificar se uma declaração existe ou se contém algum valor. O valor retornado é um valor booliano que indica se a declaração existe. O exemplo a seguir verifica se o endereço de email existe.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim**: someone@contoso.com
- Declarações de saída:
  - **outputClaim**: true

## <a name="hash"></a>Hash

Transforme o texto sem formatação fornecido em hash usando o sal e um segredo. O algoritmo de hash usado é SHA-256.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | texto não criptografado | cadeia de caracteres | A declaração de entrada a ser criptografada |
| InputClaim | sal | cadeia de caracteres | O parâmetro sal. Você pode criar um valor aleatório, usando a transformação de declarações `CreateRandomString`. |
| InputParameter | randomizerSecret | cadeia de caracteres | Aponta para uma chave de **política**de Azure ad B2C existente. Para criar uma nova chave de política: em seu locatário do Azure AD B2C, em **gerenciar**, selecione **estrutura de experiência de identidade**. Selecione **chaves de política** para exibir as chaves que estão disponíveis em seu locatário. Selecione **Adicionar**. Em **Opções** selecione **Manual**. Forneça um nome (o prefixo *B2C_1A_* pode ser adicionado automaticamente.). Na caixa de texto **segredo** , insira qualquer segredo que você queira usar, como 1234567890. Para **Uso de chave**, selecione **Assinatura**. Selecione **Criar**. |
| OutputClaim | hash | cadeia de caracteres | O ClaimType que é produzido depois de invocar esta transformação de declarações. A declaração configurada na inputClaim `plaintext`. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **texto não criptografado**: MyPass@word1
  - **sal**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Declarações de saída:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
