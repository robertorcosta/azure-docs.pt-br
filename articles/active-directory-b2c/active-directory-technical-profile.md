---
title: Defina um perfil técnico do Azure AD em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico do Azure Active Directory em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7db47eda47850c1c080b6a49256c8a0b37bb0d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330383"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do Azure Active Directory em uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) oferece suporte para o gerenciamento de usuários do Azure Active Directory. Este artigo descreve as especificações de um perfil técnico para interagir com um provedor de declarações compatível com esse protocolo padronizado.

## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `Proprietary`. O atributo **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Seguindo [o pacote de inicialização de políticas personalizadas,](custom-policy-get-started.md#custom-policy-starter-pack) os perfis técnicos do Azure AD incluem o perfil técnico **AAD-Common.** Os perfis técnicos do Azure AD não especificam o protocolo porque o protocolo está configurado no perfil técnico **AAD-Common:**
 
- **AAD-UserReadUsingAlternativeSecurityId** e **UserReadUsingAlternativeSecurityId-AAD-NoError** – procure uma conta social no diretório.
- **AAD-UserWriteUsingAlternativeSecurityId** – crie uma nova conta social.
- **AAD-UserReadUsingEmailAddress** – procure uma conta local no diretório.
- **AAD-UserWriteUsingLogonEmail** – criar uma nova conta local.
- **AAD-UserWritePasswordUsingObjectId** – atualize uma senha de uma conta local.
- **AAD-UserWriteProfileUsingObjectId** – atualize um perfil do usuário de uma conta local ou social.
- **AAD-UserReadUsingObjectId** – leia um perfil do usuário de uma conta local ou social.
- **AAD-UserWritePhoneNumberUsingObjectId** – grave o número de telefone MFA de uma conta local ou social

O exemplo a seguir mostra o perfil técnico **AAD-Common**:

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

O elemento InputClaims contém uma reclamação, que é usada para procurar uma conta no diretório ou criar uma nova. Deve haver exatamente um elemento InputClaim na coleção de reclamações de entrada para todos os perfis técnicos do Azure AD. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no Azure Active Directory.

Para ler, atualizar ou excluir uma conta de usuário existente, a solicitação de entrada é uma chave que identifica exclusivamente a conta no diretório Azure AD. Por exemplo, **objectId**, **userPrincipalName,** **signInNames.emailAddress,** **signInNames.userName**ou **alternativeSecurityId**. 

Para criar uma nova conta de usuário, a solicitação de entrada é uma chave que identifica exclusivamente uma conta local ou federada. Por exemplo, conta local: **signInNames.emailAddress**ou **signInNames.userName**. Para uma conta federada: a **alternativaSecurityId**.

O elemento [InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) pode conter uma coleção de elementos de transformação de reivindicações de entrada que são usados para modificar a reivindicação de entrada ou gerar um novo.

## <a name="outputclaims"></a>OutputClaims

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo perfil técnico do Azure AD. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no Azure Active Directory. Você também pode incluir declarações que não são retornadas pelo Azure Active Directory, desde que defina o atributo `DefaultValue`.

O elemento [OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

Por exemplo, o perfil técnico de **AAD-UserWriteUsingLogonEmail** cria uma conta local e retorna as seguintes declarações:

- **objectId**, que é o identificador da nova conta
- **newUser**, que indica se o usuário é novo
- **authenticationSource**, que define a autenticação como `localAccountAuthentication`
- **userPrincipalName**, que é o nome UPN da nova conta
- **signInNames.emailAddress**, que é o nome de entrada na conta, semelhante à declaração de entrada de **email**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

O elemento **PersistedClaims** contém todos os valores que devem ser persistidos pelo Azure AD com possíveis informações de mapeamento entre um tipo de reclamação já definido na seção [ClaimsSchema](claimsschema.md) na diretiva e o nome do atributo Azure AD.

O perfil técnico do **AAD-UserWriteUsingLogonEmail**, que cria a nova conta local, mantém as declarações a seguir:

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

O nome da declaração será o nome do atributo do Azure AD, a menos que seja especificado o atributo **PartnerClaimType**, que contém o nome de atributo do Azure AD.

## <a name="requirements-of-an-operation"></a>Requisitos de uma operação

- Deve haver exatamente uma elemento **InputClaim** na sacola de declarações para todos os perfis técnicos do Azure AD.
- O [artigo de atributos do perfil do usuário](user-profile-attributes.md) descreve os atributos de perfil de usuário AD B2C suportados que você pode usar nas reivindicações de entrada, reivindicações de saída e reclamações persistidas. 
- Se a operação for `Write` ou `DeleteClaims`, ela também deverá aparecer em um elemento **PersistedClaims**.
- O valor da declaração **userPrincipalName** deve estar no formato de `user@tenant.onmicrosoft.com`.
- A declaração **displayName** é necessária e não pode ser uma cadeia de caracteres vazia.

## <a name="azure-ad-technical-provider-operations"></a>Operações do provedor técnico do Azure AD

### <a name="read"></a>Ler

A operação **Ler** os dados sobre uma conta de usuário único. O perfil técnico a seguir lê os dados sobre uma conta de usuário usando o objectId do usuário:

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Gravar

A operação **Gravar** cria ou atualiza uma conta de usuário único. O seguinte perfil técnico cria uma nova conta social:

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

A operação **DeleteClaims** limpa as informações de uma lista de declarações fornecida. O perfil técnico a seguir exclui declarações:

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

A operação **DeleteClaimsPrincipal** exclui uma única conta de usuário do diretório. O perfil técnico a seguir exclui uma conta de usuário do diretório usando o nome UPN:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

O perfil técnico a seguir exclui uma conta de usuário social usando **alternativeSecurityId**:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | A operação a ser executada. Valores possíveis: `Read`, `Write`, `DeleteClaims` ou `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Não | Gere um erro se o objeto de usuário não existe no diretório. Valores possíveis: `true` ou `false`. |
| RaiseErrorIfClaimsPrincipalAlreadyExists | Não | Gere um erro se o objeto de usuário já existe. Valores possíveis: `true` ou `false`.|
| ApplicationObjectId | Não | O identificador de objeto de aplicativo para atributos de extensão. Valor: ObjectId de um aplicativo. Para obter mais informações, consulte [Usar atributos personalizados em uma diretiva de edição de perfil personalizado](custom-policy-custom-attributes.md). |
| ClientId | Não | O identificador de cliente para acessar o locatário como um terceiro. Para obter mais informações, veja [Atributos personalizados de uso em uma política de edição de perfil personalizada](custom-policy-custom-attributes.md) |
| IncludeClaimResolveingInClaimshandling  | Não | Para reclamações de entrada e saída, especifica se a [resolução de sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores `true`possíveis: `false`  , ou (padrão). Se você quiser usar um resolver sinistros no `true`perfil técnico, defina isso como . |

### <a name="ui-elements"></a>Elementos da interface do usuário
 
As seguintes configurações podem ser usadas para configurar a mensagem de erro exibida após a falha. Os metadados devem ser configurados no perfil técnico [auto-afirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization.md).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | Não | Se um erro for gerado (veja a descrição do atributo RaiseErrorIfClaimsPrincipalAlreadyExists), especifique a mensagem a mostrar ao usuário se o objeto de usuário já existe. |
| UserMessageIfClaimsPrincipalDoesNotExist | Não | Caso um erro deva ser gerado (veja a descrição do atributo RaiseErrorIfClaimsPrincipalDoesNotExist), especifique a mensagem para mostrar ao usuário se o objeto de usuário não existe. |


## <a name="next-steps"></a>Próximas etapas

Veja o artigo a seguir, por exemplo, usando o perfil técnico do Azure AD:

- [Adicionar declarações e personalizar a entrada do usuário usando políticas personalizadas no Azure Active Directory B2C](custom-policy-configure-user-input.md)














