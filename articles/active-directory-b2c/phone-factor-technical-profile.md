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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437459"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico do fator telefônico em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) oferece suporte para inscrição e verificação de números de telefone. Este perfil técnico:

- Fornece uma interface de usuário para interagir com o usuário para verificar ou registrar um número de telefone.
- Suporta chamadas telefônicas e mensagens de texto para validar o número de telefone.
- Suporta vários números de telefone. O usuário pode selecionar um dos números de telefone para verificar.  
- Retorna uma reclamação indicando se o usuário forneceu um novo número de telefone. Você pode usar esta reivindicação para decidir se o número de telefone deve ser persistido no perfil de usuário Azure AD B2C.  
- Usa uma [definição de conteúdo](contentdefinitions.md) para controlar a aparência.

## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `Proprietary`. O atributo **do manipulador** deve conter o nome totalmente qualificado do conjunto do manipulador de protocolo que é usado pelo Azure AD B2C para fator telefônico:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra um perfil técnico do fator telefônico para inscrição e validação:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Transformações de reivindicações de entrada

O elemento InputClaimsTransformations pode conter uma coleção de transformações de reivindicações de entrada que são usadas para modificar as reivindicações de entrada ou gerar novas. A seguinte transformação `UserId` de sinistros de entrada gera uma reivindicação que é usada posteriormente na coleta de sinistros de entrada.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Declarações de entrada

O elemento InputClaims deve conter as seguintes alegações. Você também pode mapear o nome da sua reivindicação para o nome definido no perfil técnico do fator telefone. 

|  Tipo de dados| Obrigatório | Descrição |
| --------- | -------- | ----------- | 
| string| Sim | Um identificador exclusivo do usuário. O nome da reclamação, ou PartnerClaimType deve ser definido como `UserId`. Esta alegação não deve conter informações pessoais identificáveis.|
| string| Sim | Lista de tipos de reclamações. Cada reclamação contém um número de telefone. Se alguma das reivindicações de entrada não contiver um número de telefone, o usuário será solicitado a se inscrever e verificar um novo número de telefone. O número de telefone validado é retornado como uma reivindicação de saída. Se uma das reivindicações de entrada contiver um número de telefone, o usuário é solicitado a verificar. Se várias reivindicações de entrada contiverem um número de telefone, o usuário é solicitado a escolher e verificar um dos números de telefone. |

O exemplo a seguir demonstra o uso de vários números de telefone. Para obter mais informações, consulte [a política de amostra](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento OutputClaims contém uma lista de reclamações retornadas pelo perfil técnico do fator telefone.

|  Tipo de dados| Obrigatório | Descrição |
|  -------- | ----------- |----------- |
| booleano | Sim | Indica se o novo número de telefone foi inserido pelo usuário. O nome da reclamação, ou PartnerClaimType deve ser definido como`newPhoneNumberEntered`|
| string| Sim | O número de telefone verificado. O nome da reclamação, ou PartnerClaimType deve ser definido como `Verified.OfficePhone`.|

O elemento OutputClaimsTransformations pode conter uma coleção de elementos OutputClaimsTransformation que são usados para modificar as reivindicações de saída ou gerar novas.

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** não será usado.


## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Sim | O identificador da [definição de conteúdo](contentdefinitions.md) associada com este perfil técnico. |
| Entrada manualde números de números de telefonepermitidos| Não | Especifique se um usuário pode ou não inserir manualmente um número de telefone. Valores `true`possíveis: `false` , ou (padrão).|
| configuração.autenticaçãoModo | Não | O método para validar o número de telefone. Valores `sms`possíveis: `phone` `mixed` , ou (padrão).|
| configuração.autodial| Não| Especifique se o perfil técnico deve discar automaticamente ou enviar automaticamente um SMS. Valores `true`possíveis: `false` , ou (padrão). O discagem `setting.authenticationMode` automática requer `sms`que `phone`os metadados sejam definidos para, ou . A coleção de reclamações de entrada deve ter um único número de telefone. |

### <a name="ui-elements"></a>Elementos da interface do usuário

Os elementos da interface do usuário da página de autenticação do fator telefone podem ser [localizados](localization-string-ids.md#azure-mfa-error-messages).

## <a name="next-steps"></a>Próximas etapas

- Verifique as contas sociais e locais com o pacote inicial [do MFA.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)
