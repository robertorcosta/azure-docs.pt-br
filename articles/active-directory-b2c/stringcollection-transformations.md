---
title: Exemplos de transformação de declarações StringCollection para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos de transformação de declarações StringCollection para o esquema IEF (Identity Experience Framework) de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e3ce7ff633f41ccfe6faa3cc1dba1020e74459aa
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656085"
---
# <a name="stringcollection-claims-transformations"></a>Transformações de declarações StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações de coleção de cadeia de caracteres do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Adiciona uma declaração de cadeia de caracteres a uma nova declaração de StringCollection de valores exclusivos. 

| Item | TransformationClaimType | Tipo de Dados | {1&gt;Observações&lt;1} |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | O ClaimType a ser adicionado à declaração de saída. |
| InputClaim | collection | stringCollection | [Opcional] Se especificada, a transformação de declarações copiará os itens desta coleção e adicionará o item ao final da declaração da coleção de saída. |
| OutputClaim | collection | stringCollection | O ClaimType que é produzido após essa transformação de declarações foi invocado, com o valor especificado na declaração de entrada. |

Use essa transformação de declaração para adicionar uma cadeia de caracteres a uma stringCollection nova ou existente. Ele é normalmente usado em um perfil técnico do **AAD-UserWriteUsingAlternativeSecurityId**. Antes que uma nova conta social seja criada, a transformação da declaração **CreateOtherMailsFromEmail** lê o ClaimType e adiciona o valor ao ClaimType **otherMails**.

A transformação de declarações a seguir adiciona o ClaimType **email** ao ClaimType **otherMails**.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>{1&gt;Exemplo&lt;1}

- Declarações de entrada:
  - **collection**: ["someone@outlook.com"]
  - **item**: "admin@contoso.com"
- Declarações de saída:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Adiciona um parâmetro de cadeia de caracteres a uma nova declaração StringCollection de valores exclusivos.

| Item | TransformationClaimType | Tipo de Dados | {1&gt;Observações&lt;1} |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | [Opcional] Se especificada, a transformação de declarações copiará os itens desta coleção e adicionará o item ao final da declaração da coleção de saída. |
| InputParameter | item | string | O valor a ser adicionado à declaração de saída. |
| OutputClaim | collection | stringCollection | O ClaimType que é produzido depois de invocar esta transformação de declaração, com o valor especificado no parâmetro de entrada. |

Use essa transformação de declaração para adicionar um valor de cadeia de caracteres a uma stringCollection nova ou existente. O exemplo a seguir adiciona um endereço de email constante (admin@contoso.com) à declaração **otherMails**.

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>{1&gt;Exemplo&lt;1}

- Declarações de entrada:
  - **collection**: ["someone@outlook.com"]
- Parâmetros de entrada
  - **item**: "admin@contoso.com"
- Declarações de saída:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtém o primeiro item da coleção de cadeia de caracteres fornecida.

| Item | TransformationClaimType | Tipo de Dados | {1&gt;Observações&lt;1} |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | Os ClaimTypes que são usados pela transformação de declarações para obter o item. |
| OutputClaim | extractedItem | string | Os ClaimTypes produzidos depois de invocar este ClaimsTransformation. O primeiro item na coleção. |

O exemplo a seguir lê a declaração **otherMails** e retorna o primeiro item para a declaração **email**.

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>{1&gt;Exemplo&lt;1}

- Declarações de entrada:
  - **collection**: ["someone@outlook.com", "someone@contoso.com"]
- Declarações de saída:
  - **extractedItem**: "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

Verifica se um tipo de declaração StringCollection contém um elemento

| Item | TransformationClaimType | Tipo de Dados | {1&gt;Observações&lt;1} |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | O tipo de declaração que deve ser pesquisada. |
|InputParameter|item|string|O valor a ser pesquisado.|
|InputParameter|ignoreCase|string|Especifica se essa comparação deve ignorar maiúsculas e minúsculas das cadeias de caracteres que estão sendo comparadas.|
| OutputClaim | outputClaim | booleano | O ClaimType produzido depois de invocar esta ClaimsTransformation. Um indicador booliano se a coleção contiver uma cadeia de caracteres desse tipo |

O exemplo a seguir verifica se o tipo de declaração `roles` StringCollection contém o valor de **admin**.

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

- Declarações de entrada:
    - **inputClaim**: ["leitor", "autor", "admin"]
- Parâmetros de entrada:
    - **Item**: "admin"
    - **IgnoreCase**: "true"
- Declarações de saída:
    - **outputClaim**: "verdadeiro"


