---
title: Referência de controle de exibição
titleSuffix: Azure AD B2C
description: Referência para controles de exibição Azure AD B2C. Use controles de exibição para personalizar as jornadas do usuário definidas em suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188725"
---
# <a name="display-controls"></a>Controles de exibição

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um **controle de exibição** é um elemento de interface de usuário que tem funcionalidade especial e interage com o serviço de back-end Azure Active Directory B2C (Azure AD B2C). Ele permite que o usuário execute ações na página que invocam um [perfil técnico](validation-technical-profile.md) de validação no back-end. Os controles de exibição são exibidos na página e são referenciados por um [perfil técnico auto-afirmado](self-asserted-technical-profile.md).

A imagem a seguir ilustra uma página de inscrição auto-afirmada com dois controles de exibição que validam um endereço de e-mail primário e secundário.

![Exemplo de controle de exibição renderizado](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

 Na seção [Metadados](self-asserted-technical-profile.md#metadata) de um [perfil técnico auto-afirmado,](self-asserted-technical-profile.md) `DataUri` o [ContentDefinition](contentdefinitions.md) referenciado precisa ter definido como página contrato versão 2.0.0 ou superior. Por exemplo: 

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definindo controles de exibição

O elemento **DisplayControl** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | Um identificador usado para o controle de exibição. Pode ser [referenciado](#referencing-display-controls). |
| UserInterfaceControlType | Sim | O tipo de controle de exibição. Atualmente suportado é [VerificationControl](display-control-verification.md) |

O elemento **DisplayControl** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** são usados para prepreencher o valor das reivindicações a serem coletadas do usuário. |
| Exibiralegações | 0:1 | **DisplayClaims** são usados para representar reivindicações a serem coletadas do usuário. |
| OutputClaims | 0:1 | **SaídasAs de saída** são usadas para representar reivindicações a serem salvas temporariamente para este **DisplayControl**. |
| Ações | 0:1 | **As ações** são usadas para listar os perfis técnicos de validação para invocar as ações do usuário que acontecem no front-end. |

### <a name="input-claims"></a>Declarações de entrada

Em um controle de exibição, você pode usar elementos **InputClaims** para prepreencher o valor das reivindicações a serem coletadas do usuário na página. Quaisquer **InputClaimsTransformations** podem ser definidos no perfil técnico auto-afirmado que faz referência a esse controle de exibição.

O exemplo a seguir prepreenche o endereço de e-mail a ser verificado com o endereço já presente.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Reivindicações de exibição

Cada tipo de controle de exibição requer um conjunto diferente de reivindicações de [exibição, reivindicações de saída](#output-claims)e [ações](#display-control-actions) a serem executadas.

Semelhante às alegações de **exibição** definidas em um [perfil técnico auto-afirmado,](self-asserted-technical-profile.md#display-claims)as reivindicações de exibição representam as reivindicações a serem coletadas do usuário dentro do controle de exibição. O elemento **ClaimType** referenciado precisa especificar o elemento **UserInputType** para um tipo de entrada `TextBox` de `DropdownSingleSelect`usuário suportado pelo Azure AD B2C, como ou . Se um valor de reclamação de exibição for `true` exigido por uma **Ação,** defina o atributo **necessário** para forçar o usuário a fornecer um valor para essa reclamação de exibição específica.

Certas reivindicações de exibição são necessárias para certos tipos de controle de exibição. Por exemplo, **VerificationCode** é necessário para o controle de exibição do Type **VerificationControl**. Use o atributo **ControlClaimType** para especificar qual DisplayClaim é designado para a reclamação necessária. Por exemplo: 

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Declarações de saída

As **alegações** de saída de um controle de exibição não são enviadas para a próxima etapa de orquestração. Eles são salvos temporariamente apenas para a sessão de controle de exibição atual. Essas reivindicações temporárias podem ser compartilhadas entre as diferentes ações do mesmo controle de exibição.

Para borbulhar as reivindicações de saída para a próxima etapa de orquestração, use as **OutputClaims** do perfil técnico auto-afirmado real que faz referência a esse controle de exibição.

### <a name="display-control-actions"></a>Ações de controle de exibição

As **ações** de um controle de exibição são procedimentos que ocorrem no back-end Ad B2C do Azure Quando um usuário executa uma determinada ação no lado do cliente (o navegador). Por exemplo, as validações a serem aplicadas quando o usuário seleciona um botão na página.

Uma ação define uma lista de **perfis técnicos**de validação . Eles são usados para validar algumas ou todas as reivindicações de exibição do controle de exibição. O perfil técnico de validação valida a entrada do usuário e pode retornar um erro ao usuário. Você pode usar **ContinueOnError,** **ContinueOnSuccess**e **Preconditions** no controle de exibição Ação semelhante à maneira como eles são usados em [perfis técnicos de validação](validation-technical-profile.md) em um perfil técnico auto-afirmado.

O exemplo a seguir envia um código em e-mail ou SMS com base na seleção do usuário da reivindicação **mfaType.**

```XML
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Referenciamento de controles de exibição

Os controles de exibição são referenciados nas [alegações](self-asserted-technical-profile.md#display-claims) de exibição do [perfil técnico auto-afirmado](self-asserted-technical-profile.md).

Por exemplo: 

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
