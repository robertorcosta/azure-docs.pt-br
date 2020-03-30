---
title: Defina um perfil técnico de transformação de Sinistros
titleSuffix: Azure AD B2C
description: Definir um perfil técnico de transformação de Declarações em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84c1cf798e88e4067da8a495c1591143d2ee1bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189779"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico de transformação de declarações em uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico de transformação de declarações permite que você chame transformações de declarações de saída para manipular valores de declarações, validar declarações ou definir valores padrão para um conjunto de declarações de saída.

## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `Proprietary`. O atributo **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo usado pelo Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

O exemplo a seguir mostra um perfil técnico de transformação de declarações:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** é obrigatório. Você deve fornecer pelo menos uma declaração de saída retornada pelo perfil técnico. O exemplo a seguir mostra como definir valores padrão nas declarações de saída:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Transformações de declarações de saída

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações ou para gerar novas declarações. O perfil técnico a seguir chama a transformação de declarações **RemoveAlternativeSecurityIdByIdentityProvider**. Essa transformação de declarações remove uma identidade social da coleção de **AlternativeSecurityIds**. As declarações de saída desse perfil técnico são **identityProvider2**, que é definido como `facebook.com`, e **AlternativeSecurityIds**, que contém a lista de identidades sociais associadas a este usuário após a remoção da identidade do facebook.com.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

O perfil técnico da transformação de declarações permite que você execute uma transformação de declarações em qualquer etapa da orquestração do percurso do usuário. No exemplo a seguir, a etapa de orquestração chama um dos perfis técnicos de desvinculação, como **UnLink-Facebook-OAUTH**. Esse perfil técnico chama o perfil técnico de transformação de sinistros **removeAlternativeSecurityIdByIdentityProvider**, que gera uma nova alegação **AlternativeSecurityIds2** que contém a lista de identidades sociais do usuário, enquanto remove a identidade do Facebook das coleções.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| IncludeClaimResolveingInClaimshandling  | Não | Para reclamações de entrada e saída, especifica se a [resolução de sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores `true`possíveis: `false`  , ou (padrão). Se você quiser usar um resolver sinistros no `true`perfil técnico, defina isso como . |

## <a name="use-a-validation-technical-profile"></a>Usar o perfil técnico de validação

Um perfil técnico de transformação de declarações pode ser usado para validar informações. No exemplo a seguir, o [perfil técnico autodeclarado](self-asserted-technical-profile.md) chamado **LocalAccountSignUpWithLogonEmail** pede que o usuário insira o email duas vezes e, em seguida, chama o [perfil técnico de validação](validation-technical-profile.md) chamado **Validade-Email** para validar os emails. O perfil técnico **Validate-Email** chamará a transformação de declarações **AssertEmailAreEqual** para comparar as duas declarações **email** e **emailRepeat** e lançar uma exceção se elas não forem iguais de acordo com a comparação especificada.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

O perfil técnico da transformação de declarações chama a transformação de declarações **AssertEmailAreEqual**, que declara que os emails fornecidos pelo usuário são os mesmos.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Um perfil técnico autodeclarado pode chamar o perfil técnico de validação e mostrar a mensagem de erro, conforme especificado nos metadados **UserMessageIfClaimsTransformationStringsAreNotEqual**.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
