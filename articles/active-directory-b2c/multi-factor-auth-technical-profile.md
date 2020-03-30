---
title: Perfis técnicos do Azure MFA em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para perfis técnicos de autenticação multifatorial (MFA) do Azure no Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332815"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico Do Azure MFA em uma política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para verificar um número de telefone usando o MFA (Multi-Factor Authentication, autenticação multifatorial do Azure). Use este perfil técnico para gerar e enviar um código para um número de telefone e, em seguida, verifique o código. O perfil técnico do Azure MFA também pode retornar uma mensagem de erro.  O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário continue. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página auto-afirmada.

Este perfil técnico:

- Não fornece uma interface para interagir com o usuário. Em vez disso, a interface de usuário é chamada a partir de um perfil técnico [auto-afirmado,](self-asserted-technical-profile.md) ou um controle de [exibição](display-controls.md) como um [perfil técnico](validation-technical-profile.md)de validação .
- Usa o serviço MFA do Azure para gerar e enviar um código para um número de telefone e, em seguida, verifica o código.  
- Valida um número de telefone através de mensagens de texto.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `Proprietary`. O atributo **do manipulador** deve conter o nome totalmente qualificado do conjunto do manipulador de protocolo que é usado pelo Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico do Azure MFA:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Enviar SMS

O primeiro modo deste perfil técnico é gerar um código e enviá-lo. As seguintes opções podem ser configuradas para este modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de reivindicações a serem enviadas ao Azure MFA. Você também pode mapear o nome da sua reivindicação para o nome definido no perfil técnico do MFA.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| userPrincipalName | Sim | O identificador para o usuário que possui o número de telefone. |
| phoneNumber | Sim | O número de telefone para enviar um código SMS. |
| companyName | Não |O nome da empresa no SMS. Se não for fornecido, o nome da sua aplicação é usado. |
| localidade | Não | A localização do SMS. Se não for fornecido, a localização do navegador do usuário é usada. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reivindicações de entrada ou gerar novas antes de enviar para o serviço MFA do Azure.

### <a name="output-claims"></a>Declarações de saída

O provedor de protocolo Azure MFA não retorna quaisquer **OutputClaims,** portanto, não há necessidade de especificar reivindicações de saída. Você pode, no entanto, incluir reivindicações que não são devolvidas pelo provedor `DefaultValue` de identidade MFA do Azure, desde que você defina o atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **OneWaySMS.**  |

#### <a name="ui-elements"></a>Elementos da interface do usuário

Os seguintes metadados podem ser usados para configurar as mensagens de erro exibidas ao enviar falha de SMS. Os metadados devem ser configurados no perfil técnico [auto-afirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization-string-ids.md#azure-mfa-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | Não | Mensagem de erro do usuário se o número de telefone fornecido não aceitar SMS. |
| Mensagens de usuárioFormatodeinvalidação | Não | Mensagem de erro do usuário se o número de telefone fornecido não for um número de telefone válido. |
| Mensagens de usuárioErro de servidor | Não | Mensagem de erro do usuário se o servidor tiver encontrado um erro interno. |
| Mensagens de usuárioIfThrottled| Não | Mensagem de erro do usuário se uma solicitação tiver sido estrangulada.|

### <a name="example-send-an-sms"></a>Exemplo: envie um SMS

O exemplo a seguir mostra um perfil técnico do Azure MFA que é usado para enviar um código via SMS.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificar código

O segundo modo deste perfil técnico é verificar um código. As seguintes opções podem ser configuradas para este modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de reivindicações a serem enviadas ao Azure MFA. Você também pode mapear o nome da sua reivindicação para o nome definido no perfil técnico do MFA.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Sim | Mesmo número de telefone usado anteriormente para enviar um código. Ele também é usado para localizar uma sessão de verificação telefônica. |
| verificaçãoCódigo  | Sim | O código de verificação fornecido pelo usuário a ser verificado |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reivindicações de entrada ou gerar novas antes de chamar o serviço MFA do Azure.

### <a name="output-claims"></a>Declarações de saída

O provedor de protocolo Azure MFA não retorna quaisquer **OutputClaims,** portanto, não há necessidade de especificar reivindicações de saída. Você pode, no entanto, incluir reivindicações que não são devolvidas pelo provedor `DefaultValue` de identidade MFA do Azure, desde que você defina o atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **Verificar** |

#### <a name="ui-elements"></a>Elementos da interface do usuário

Os seguintes metadados podem ser usados para configurar as mensagens de erro exibidas após falha na verificação de código. Os metadados devem ser configurados no perfil técnico [auto-afirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization-string-ids.md#azure-mfa-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Mensagens de usuárioIfMaxAllowedCodeRetryReached| Não | Mensagem de erro do usuário se o usuário tentou um código de verificação muitas vezes. |
| Mensagens de usuárioErro de servidor | Não | Mensagem de erro do usuário se o servidor tiver encontrado um erro interno. |
| Mensagens de usuárioIfThrottled| Não | Mensagem de erro do usuário se a solicitação for estrangulada.|
| UserMessageIfWrongCodeEntered| Não| Mensagem de erro do usuário se o código inserido para verificação estiver errado.|

### <a name="example-verify-a-code"></a>Exemplo: verifique um código

O exemplo a seguir mostra um perfil técnico Do Azure MFA usado para verificar o código.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
