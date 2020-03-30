---
title: StringCollection reivindica exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: StringCollection reivindica exemplos de transformação para o esquema IEF (Identity Experience Framework, estrutura de experiência de identidade) do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6aea537ebff4ae61e00861e6cafe742a7feb165e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186770"
---
# <a name="stringcollection-claims-transformations"></a>Transformações de declarações StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para o uso das transformações de reivindicações de coleta de strings do esquema Identity Experience Framework no Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Adiciona uma reivindicação de string a uma nova reivindicação de string de valores exclusivos.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | O ClaimType a ser adicionado à declaração de saída. |
| InputClaim | collection | stringCollection | [Opcional] Se especificada, a transformação de declarações copiará os itens desta coleção e adicionará o item ao final da declaração da coleção de saída. |
| OutputClaim | collection | stringCollection | O Tipo de Reclamação que é produzido após essa transformação de sinistros foi invocado, com o valor especificado na reivindicação de entrada. |

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

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **coleção:**["someone@outlook.com"]
  - **item**:admin@contoso.com" "
- Declarações de saída:
  - **coleção:**["someone@outlook.comadmin@contoso.com", ""

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Adiciona um parâmetro de string a uma nova reivindicação de string de valores exclusivos.

| Item | TransformationClaimType | Tipo de Dados | Observações |
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

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **coleção:**["someone@outlook.com"]
- Parâmetros de entrada
  - **item**:admin@contoso.com" "
- Declarações de saída:
  - **coleção:**["someone@outlook.comadmin@contoso.com", ""

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtém o primeiro item da coleção de cadeia de caracteres fornecida.

| Item | TransformationClaimType | Tipo de Dados | Observações |
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

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **coleção:**["someone@outlook.comsomeone@contoso.com", ""
- Declarações de saída:
  - **extractedItem**:someone@outlook.com" "


## <a name="stringcollectioncontains"></a>StringCollectioncontém

Verifica se um tipo de reclamação stringcollection contém um elemento

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | O tipo de sinistro que deve ser pesquisado. |
|InputParameter|item|string|O valor para pesquisar.|
|InputParameter|ignoreCase|string|Especifica se essa comparação deve ignorar maiúsculas e minúsculas das cadeias de caracteres que estão sendo comparadas.|
| OutputClaim | outputClaim | booleano | O ClaimType produzido depois de invocar esta ClaimsTransformation. Um indicador booleano se a coleção contém tal string |

O exemplo a `roles` seguir verifica se o tipo de reclamação stringCollection contém o valor do **admin**.

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
    - **item**: "Admin"
    - **ignorarCase**: "verdadeiro"
- Declarações de saída:
    - **saídaReclamação:**"verdadeiro"


