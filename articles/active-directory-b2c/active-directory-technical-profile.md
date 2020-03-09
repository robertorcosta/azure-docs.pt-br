---
title: Definir um perfil técnico do Azure AD em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico do Azure Active Directory em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 82daf447270fc0413284e3e7a908a8b5237a4f9c
ms.sourcegitcommit: 3616b42a0d6bbc31b965995d861930e53d2cf0d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78932967"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do Azure Active Directory em uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para o gerenciamento de usuários do Azure Active Directory. Este artigo descreve as especificações de um perfil técnico para interagir com um provedor de declarações compatível com esse protocolo padronizado.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Os seguintes perfis técnicos do Azure AD [Starter Pack de política personalizada](custom-policy-get-started.md#custom-policy-starter-pack) incluem o perfil técnico do **AAD-comum** . Os perfis técnicos do Azure AD não especificam o protocolo porque o protocolo está configurado no perfil técnico **comum do AAD** :
 
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

## <a name="input-claims"></a>Declarações de entrada

O elemento InputClaims contém uma declaração, que é usada para pesquisar uma conta no diretório ou criar uma nova. Deve haver exatamente um elemento InputClaim na coleção de declarações de entrada para todos os perfis técnicos do Azure AD. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no Azure Active Directory.

Para ler, atualizar ou excluir uma conta de usuário existente, a declaração de entrada é uma chave que identifica exclusivamente a conta no diretório do AD do Azure. Por exemplo, **ObjectID**, **userPrincipalName**, **signInNames. EmailAddress**, **signInNames. username**ou **alternativeSecurityId**. 

Para criar uma nova conta de usuário, a declaração de entrada é uma chave que identifica exclusivamente uma conta local ou federada. Por exemplo, conta local: **signInNames. EmailAddress**ou **signInNames. username**. Para uma conta federada: o **alternativeSecurityId**.

O elemento InputClaimsTransformations pode conter uma coleção de elementos de transformação de declarações de entrada que são usados para modificar a declaração de entrada ou gerar uma nova.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo perfil técnico do Azure AD. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no Azure Active Directory. Você também pode incluir declarações que não são retornadas pelo Azure Active Directory, desde que defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

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

O elemento **PersistedClaims** contém todos os valores que o Azure AD deve manter, com possíveis informações de mapeamento entre um tipo de declaração já definido na seção ClaimsSchema na política e o nome do atributo do Azure AD.

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
- Se a operação for `Write` ou `DeleteClaims`, ela também deverá aparecer em um elemento **PersistedClaims**.
- O valor da declaração **userPrincipalName** deve estar no formato de `user@tenant.onmicrosoft.com`.
- A declaração **displayName** é necessária e não pode ser uma cadeia de caracteres vazia.

## <a name="azure-ad-technical-provider-operations"></a>Operações do provedor técnico do Azure AD

### <a name="read"></a>Ler

A operação **Ler** os dados sobre uma conta de usuário único. Para ler dados do usuário, você precisa fornecer uma chave como uma declaração de entrada, como **objectId**, **userPrincipalName**, **signInNames** (qualquer tipo, nome de usuário e conta baseada em email) ou **alternativeSecurityId**.

O perfil técnico a seguir lê os dados sobre uma conta de usuário usando o objectId do usuário:

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

### <a name="write"></a>Gravação

A operação **Gravar** cria ou atualiza uma conta de usuário único. Para gravar uma conta de usuário, você precisa fornecer uma chave como uma declaração de entrada, como **objectId**, **userPrincipalName**, **signInNames.emailAddress** ou **alternativeSecurityId**.

O seguinte perfil técnico cria uma nova conta social:

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

A operação **DeleteClaims** limpa as informações de uma lista de declarações fornecida. Para excluir informações de declarações, você precisa fornecer uma chave como uma declaração de entrada, como **objectId**, **userPrincipalName**, **signInNames.emailAddress** ou **alternativeSecurityId**.

O perfil técnico a seguir exclui declarações:

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

A operação **DeleteClaimsPrincipal** exclui uma única conta de usuário do diretório. Para excluir uma conta de usuário, você precisa fornecer uma chave como uma declaração de entrada, como **objectId**, **userPrincipalName**, **signInNames.emailAddress** ou **alternativeSecurityId**.

O perfil técnico a seguir exclui uma conta de usuário do diretório usando o nome UPN:

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
| UserMessageIfClaimsPrincipalDoesNotExist | Não | Caso um erro deva ser gerado (veja a descrição do atributo RaiseErrorIfClaimsPrincipalDoesNotExist), especifique a mensagem para mostrar ao usuário se o objeto de usuário não existe. O valor pode ser [localizado](localization.md).|
| RaiseErrorIfClaimsPrincipalAlreadyExists | Não | Gere um erro se o objeto de usuário já existe. Valores possíveis: `true` ou `false`.|
| UserMessageIfClaimsPrincipalAlreadyExists | Não | Se um erro for gerado (veja a descrição do atributo RaiseErrorIfClaimsPrincipalAlreadyExists), especifique a mensagem a mostrar ao usuário se o objeto de usuário já existe. O valor pode ser [localizado](localization.md).|
| ApplicationObjectId | Não | O identificador de objeto de aplicativo para atributos de extensão. Valor: ObjectId de um aplicativo. Para obter mais informações, veja [Atributos personalizados de uso em uma política de edição de perfil personalizada](custom-policy-custom-attributes.md). |
| ClientId | Não | O identificador de cliente para acessar o locatário como um terceiro. Para obter mais informações, veja [Atributos personalizados de uso em uma política de edição de perfil personalizada](custom-policy-custom-attributes.md) |
| IncludeClaimResolvingInClaimsHandling  | Não | Para declarações de entrada e saída, especifica se a [resolução de declarações](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true`ou `false` (padrão). Se você quiser usar um resolvedor de declarações no perfil técnico, defina isso como `true`. |














