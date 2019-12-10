---
title: Definir um perfil técnico autodeclarado em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico autodeclarado em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bfa8982fb49b31540d1926bdeb75a96dc1d79cf0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950894"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico autodeclarado em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Todas as interações em Azure Active Directory B2C (Azure AD B2C) em que o usuário deve fornecer entrada são perfis técnicos autodeclarados. Por exemplo, uma página de inscrição, entrada ou redefinição de senha.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo usado pelo Azure AD B2C, para autodeclaração: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra um perfil técnico autodeclarado para email de inscrição:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Declarações de entrada

Em um perfil técnico autodeclarado, é possível usar os elementos **InputClaims** e **InputClaimsTransformations** para preencher previamente o valor das declarações que aparecem na página autodeclarada (declarações de saída). Por exemplo, na política de edição de perfil, o percurso do usuário primeiro lê o perfil do usuário do serviço de diretório do Azure AD B2C. Em seguida, o perfil técnico autodeclarado define as declarações de entrada com os dados do usuário armazenados no perfil do usuário. Essas declarações são coletadas do perfil do usuário e, em seguida, apresentadas a ele, que poderá editar os dados existentes.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações a serem apresentadas para coletar dados do usuário. Para preencher previamente as declarações de saída com alguns valores, use as declarações de entrada descritas anteriormente. O elemento também pode conter um valor padrão. A ordem das declarações no **OutputClaims** controla a ordem em que o Azure AD B2C renderiza as declarações na tela. O atributo **DefaultValue** entrará em vigor somente se a declaração nunca tiver sido definida antes. No entanto, se tiver sido definido em uma etapa anterior de orquestração, mesmo se o usuário deixar o valor vazio, o valor padrão não entrará em vigor. Para forçar o uso de um valor padrão, defina o atributo **AlwaysUseDefaultValue** como `true`. Para forçar o usuário a fornecer um valor para uma declaração de saída específica, defina o atributo **Required** do elemento **OutputClaims** como `true`.

O elemento **ClaimType** da coleção **OutputClaims** precisa ser definir o elemento **UserInputType** como qualquer tipo de entrada do usuário compatível com o Azure AD B2C, como `TextBox` ou `DropdownSingleSelect`. Outra opção é o elemento **OutputClaim** definir um **DefaultValue**.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

A seguinte declaração de saída é sempre definida como `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Caso de uso

Há quatro cenários para declarações de saída:

- **Coletar as declarações de saída do usuário**: se você precisar coletar informações do usuário, como a data de nascimento, adicione a declaração à coleção **OutputClaims**. As declarações apresentadas para o usuário precisam especificar o **UserInputType**, como `TextBox` ou `DropdownSingleSelect`. Se o perfil técnico autodeclarado contiver um perfil técnico de validação que gera a mesma declaração, o Azure AD B2C não apresentará a declaração para o usuário. Se não houver nenhuma declaração de saída para apresentar ao usuário, o Azure AD B2C ignorará o perfil técnico.
- **Definir um valor padrão em uma declaração de saída**: sem a coleta de dados do usuário ou retornar os dados do perfil técnico de validação. O perfil técnico autodeclarado `LocalAccountSignUpWithLogonEmail` define a declaração **executed-SelfAsserted-Input** como `true`.
- **Um perfil técnico de validação retorna as declarações de saída**: seu perfil técnico pode chamar um perfil técnico de validação que retorna algumas declarações. Talvez você queira juntar as declarações e retorná-las para as próximas etapas de orquestração no percurso do usuário. Por exemplo, ao entrar com uma conta local, o perfil técnico autodeclarado de nome `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação chamado `login-NonInteractive`. Esse perfil técnico valida as credenciais do usuário e também retorna o perfil do usuário. Como 'userPrincipalName', 'displayName', 'givenName' e 'surName'.
- **Enviar as declarações por meio da transformação das declarações de saída**

No exemplo a seguir, o perfil técnico autodeclarado `LocalAccountSignUpWithLogonEmail` demonstra o uso de declarações de saída e define **executed-SelfAsserted-Input** como `true`. As declarações `objectId`, `authenticationSource` e `newUser` são a saída do perfil técnico de validação `AAD-UserWriteUsingLogonEmail` e não são mostradas para o usuário.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Declarações de persistência

Se o elemento **PersistedClaims** estiver ausente, o perfil técnico autodeclarado não persistirá os dados para o Azure AD B2C. Em vez disso, é feita uma chamada para um perfil técnico de validação responsável por persistir os dados. Por exemplo, a política de inscrição usa o perfil técnico autodeclarado `LocalAccountSignUpWithLogonEmail` para coletar o novo perfil do usuário. O perfil técnico `LocalAccountSignUpWithLogonEmail` chama o perfil técnico de validação para criar a conta no Azure AD B2C.

## <a name="validation-technical-profiles"></a>Perfis técnicos de validação

Um perfil técnico de validação é usado para validar algumas ou todas as declarações de saída do perfil técnico de referência. As declarações de entrada do perfil técnico de validação precisam aparecer nas declarações de saída do perfil técnico autodeclarado. O perfil técnico de validação valida a entrada do usuário e pode retornar um erro ao usuário.

O perfil técnico de validação pode ser qualquer perfil técnico na política, como perfis técnicos do [Azure Active Directory](active-directory-technical-profile.md) ou da [API REST](restful-technical-profile.md). No exemplo anterior, o perfil técnico `LocalAccountSignUpWithLogonEmail` valida que o signinName não existe no diretório. Caso contrário, o perfil técnico de validação cria uma conta local e retorna objectId, authenticationSource e newUser. O perfil técnico `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação `login-NonInteractive` para validar as credenciais do usuário.

Também é possível chamar um perfil técnico da API REST com a lógica de negócios, substituir as declarações de entrada ou aprimorar os dados do usuário integrando ainda mais com aplicativos de linha de negócios corporativos. Para saber mais, confira [Perfil técnico de validação](validation-technical-profile.md)

## <a name="metadata"></a>Metadados

| Atributo | obrigatórios | Descrição |
| --------- | -------- | ----------- |
| setting.showContinueButton | Não | Mostra o botão continuar. Valores possíveis: `true` (padrão) ou `false` |
| setting.showCancelButton | Não | Mostra o botão cancelar. Valores possíveis: `true` (padrão) ou `false` |
| setting.operatingMode | Não | Em uma página de entrada, essa propriedade controla o comportamento do campo nome de usuário, como validação de entradas e mensagens de erro. Valores esperados: `Username` ou `Email`. |
| ContentDefinitionReferenceId | SIM | O identificador da [definição de conteúdo](contentdefinitions.md) associada com este perfil técnico. |
| EnforceEmailVerification | Não | Na inscrição ou edição de perfil, reforça a verificação de email. Valores possíveis: `true` (padrão) ou `false`. |
| setting.showSignupLink | Não | Mostra o botão de inscrição. Valores possíveis: `true` (padrão) ou `false` |
| setting.retryLimit | Não | Controla a quantidade de vezes que um usuário pode tentar fornecer os dados verificados pelo perfil técnico de validação. Por exemplo, quanto um usuário tenta se inscrever com uma conta que já existe e continua tentando até alcançar o limite.
| SignUpTarget | Não | O identificador de troca do destino da inscrição. Quando o usuário clica no botão de inscrição, o Azure AD B2C executa o identificador de troca especificado. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** não será usado.













