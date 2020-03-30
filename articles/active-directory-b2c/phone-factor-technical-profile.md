---
title: Defina um perfil técnico do fator telefônico em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico do fator telefônico em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332664"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico do fator telefônico em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) oferece suporte para inscrição e verificação de números de telefone. Este perfil técnico:

- Fornece uma interface de usuário para interagir com o usuário.
- Usa a definição de conteúdo para controlar a aparência.
- Suporta chamadas telefônicas e mensagens de texto para validar o número de telefone.
- Suporta vários números de telefone. O usuário pode selecionar um dos números de telefone para verificar.  
- Se um número de telefone for fornecido, a interface do usuário do fator telefone pede ao usuário para verificar o número de telefone. Se não for fornecido, ele pede ao usuário para cadastrar um novo número de telefone.
- Retorna uma reclamação indicando se o usuário forneceu um novo número de telefone. Você pode usar esta reivindicação para decidir se o número de telefone deve ser persistido no perfil de usuário do Azure AD.  

## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `Proprietary`. O atributo **do manipulador** deve conter o nome totalmente qualificado do conjunto do manipulador de protocolo que é usado pelo Azure AD B2C para fator telefônico:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra um perfil técnico do fator telefônico para inscrição e validação:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Declarações de entrada

O elemento InputClaims deve conter as seguintes reivindicações. Você também pode mapear o nome da sua reivindicação para o nome definido no perfil técnico do fator telefone. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

O exemplo a seguir demonstra o uso de vários números de telefone. Para obter mais informações, consulte [a política de amostra](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

O elemento InputClaimsTransformations pode conter uma coleção de elementos inputClaimsTransformation que são usados para modificar as reivindicações de entrada ou gerar novas antes de apresentá-las à página do fator telefone.

## <a name="output-claims"></a>Declarações de saída

O elemento OutputClaims contém uma lista de reclamações retornadas pelo perfil técnico do fator telefone.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

O elemento OutputClaimsTransformations pode conter uma coleção de elementos OutputClaimsTransformation usados para modificar as declarações de saída ou gerar novas declarações.

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** não será usado.


## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Sim | O identificador da [definição de conteúdo](contentdefinitions.md) associada com este perfil técnico. |
| Entrada manualde números de números de telefonepermitidos| Não | Especifique se um usuário pode ou não inserir manualmente um número de telefone. Valores `true` possíveis: ou `false` (padrão).|

### <a name="ui-elements"></a>Elementos da interface do usuário

Os elementos da interface do usuário da página de autenticação do fator telefone podem ser [localizados](localization-string-ids.md#azure-mfa-error-messages).

## <a name="next-steps"></a>Próximas etapas

- Verifique as contas sociais e locais com o pacote inicial [do MFA.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)

