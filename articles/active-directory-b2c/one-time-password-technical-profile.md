---
title: Habilite a verificação de senha única (OTP)
titleSuffix: Azure AD B2C
description: Saiba como configurar um cenário de senha única (OTP) usando políticas personalizadas do Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332773"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico de senha única em uma política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) oferece suporte para gerenciar a geração e a verificação de uma senha única. Use um perfil técnico para gerar um código e, em seguida, verifique esse código mais tarde.

O perfil técnico de senha única também pode retornar uma mensagem de erro durante a verificação do código. Projete a integração com a senha única usando um **perfil técnico de validação**. Um perfil técnico de validação chama o perfil técnico de senha única para verificar um código. O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário continue. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página auto-afirmada.

## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `Proprietary`. O atributo **do manipulador** deve conter o nome totalmente qualificado do conjunto do manipulador de protocolo que é usado pelo Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico de senha única:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Gerar código

O primeiro modo deste perfil técnico é gerar um código. Abaixo estão as opções que podem ser configuradas para este modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de reclamações necessárias para enviar ao provedor de protocolo de senha única. Você também pode mapear o nome da sua reivindicação para o nome definido abaixo.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| identificador | Sim | O identificador para identificar o usuário que precisa verificar o código mais tarde. É comumente usado como o identificador do destino onde o código é entregue, por exemplo, endereço de e-mail ou número de telefone. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reivindicações de entrada ou gerar novas antes de enviar para o provedor de protocolo de senha única.

### <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de reclamações geradas pelo provedor de protocolo de senha única. Você também pode mapear o nome da sua reivindicação para o nome definido abaixo.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| otpGenerated | Sim | O código gerado cuja sessão é gerenciada pelo Azure AD B2C. |

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

As seguintes configurações podem ser usadas para configurar o modo de geração de código:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| CodeExpirationinseconds | Não | Tempo em segundos até a expiração do código. Mínimo: `60`; Máximo: `1200`; Padrão: `600`. |
| Codelength | Não | Comprimento do código. O valor padrão é `6`. |
| CharacterSet | Não | O caractere definido para o código, formatado para uso em uma expressão regular. Por exemplo, `a-z0-9A-Z`. O valor padrão é `0-9`. O conjunto de caracteres deve incluir um mínimo de 10 caracteres diferentes no conjunto especificado. |
| Tentativas de NumRetry | Não | O número de tentativas de verificação antes do código é considerado inválido. O valor padrão é `5`. |
| Operação | Sim | A operação a ser executada. Valor possível: `GenerateCode`. |
| ReuseSameCode | Não | Se um código duplicado deve ser dado em vez de gerar um novo código quando o código dado não expirou e ainda é válido. O valor padrão é `false`. |

### <a name="example"></a>Exemplo

O exemplo `TechnicalProfile` a seguir é usado para gerar um código:

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificar código

O segundo modo deste perfil técnico é verificar um código. Abaixo estão as opções que podem ser configuradas para este modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de reclamações necessárias para enviar ao provedor de protocolo de senha única. Você também pode mapear o nome da sua reivindicação para o nome definido abaixo.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| identificador | Sim | O identificador para identificar o usuário que já gerou um código. É comumente usado como o identificador do destino onde o código é entregue, por exemplo, endereço de e-mail ou número de telefone. |
| otpToVerify | Sim | O código de verificação fornecido pelo usuário. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reivindicações de entrada ou gerar novas antes de enviar para o provedor de protocolo de senha única.

### <a name="output-claims"></a>Declarações de saída

Não há reclamações de saída fornecidas durante a verificação de código deste provedor de protocolo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

As seguintes configurações podem ser usadas para o modo de verificação de código:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | A operação a ser executada. Valor possível: `VerifyCode`. |


### <a name="ui-elements"></a>Elementos da interface do usuário

Os seguintes metadados podem ser usados para configurar as mensagens de erro exibidas após falha na verificação de código. Os metadados devem ser configurados no perfil técnico [auto-afirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization-string-ids.md#one-time-password-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfSessionnão existe | Não | A mensagem a ser exibida ao usuário se a sessão de verificação de código tiver expirado. Ou o código expirou ou o código nunca foi gerado para um determinado identificador. |
| UserMessageIfMaxRetryTrytry | Não | A mensagem a ser exibida ao usuário se eles excederam as tentativas máximas de verificação permitidas. |
| Mensagens de usuárioIfInvalidCode | Não | A mensagem a ser exibida ao usuário se ele forneceu um código inválido. |
|Mensagens de usuárioConflitodesessão de sessão|Não| A mensagem a ser exibida ao usuário se o código não puder ser verificado.|

### <a name="example"></a>Exemplo

O exemplo `TechnicalProfile` a seguir é usado para verificar um código:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Próximas etapas

Veja o artigo a seguir, por exemplo, sobre o uso de perfil técnico de senha única com verificação personalizada de e-mail:

- [Verificação personalizada de e-mail no Azure Active Directory B2C](custom-email.md)

