---
title: Defina um perfil técnico auto-afirmado em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico autodeclarado em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b29b8b0975639e5c5315a55e1382794d7662665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332503"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico autodeclarado em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Todas as interações no Azure Active Directory B2C (Azure AD B2C) onde o usuário deve fornecer entrada são perfis técnicos auto-afirmados. Por exemplo, uma página de inscrição, entrada ou redefinição de senha.

## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `Proprietary`. O atributo **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo usado pelo Azure AD B2C, para autodeclaração: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra um perfil técnico autodeclarado para email de inscrição:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Declarações de entrada

Em um perfil técnico auto-afirmado, você pode usar os elementos **InputClaims** e **InputClaimsTransformations** para prepreencher o valor das reivindicações que aparecem na página auto-afirmada (exibir reclamações). Por exemplo, na política de edição de perfil, o percurso do usuário primeiro lê o perfil do usuário do serviço de diretório do Azure AD B2C. Em seguida, o perfil técnico autodeclarado define as declarações de entrada com os dados do usuário armazenados no perfil do usuário. Essas declarações são coletadas do perfil do usuário e, em seguida, apresentadas a ele, que poderá editar os dados existentes.

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

## <a name="display-claims"></a>Reivindicações de exibição

O recurso de reivindicações de exibição está atualmente em **pré-visualização**.

O elemento **DisplayClaims** contém uma lista de reivindicações a serem apresentadas na tela para coleta de dados do usuário. Para pré-preencher os valores das reivindicações de exibição, use as reivindicações de entrada descritas anteriormente. O elemento também pode conter um valor padrão.

A ordem das reclamações no **DisplayClaims** especifica a ordem na qual o Azure AD B2C renderiza as reivindicações na tela. Para forçar o usuário a fornecer um valor para uma reclamação específica, defina o atributo **Obrigatório** do elemento **DisplayClaim** como `true`.

O elemento **ClaimType** na coleção **DisplayClaims** precisa definir o elemento **UserInputType** para qualquer tipo de entrada do usuário suportado pelo Azure AD B2C. Por exemplo, `TextBox` ou `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Adicione uma referência a um DisplayControl

Na coleção de reivindicações de exibição, você pode incluir uma referência a um [DisplayControl](display-controls.md) que você criou. Um controle de exibição é um elemento de interface de usuário que tem funcionalidade especial e interage com o serviço de back-end AD B2C do Azure. Ele permite que o usuário execute ações na página que invocam um perfil técnico de validação no back-end. Por exemplo, verificar um endereço de e-mail, número de telefone ou número de fidelização de clientes.

O exemplo `TechnicalProfile` a seguir ilustra o uso de reivindicações de exibição com controles de exibição.

* A primeira reclamação de exibição faz uma referência ao controle de `emailVerificationControl` exibição, que coleta e verifica o endereço de e-mail.
* A quinta reivindicação de exibição faz uma referência ao controle de `phoneVerificationControl` exibição, que coleta e verifica um número de telefone.
* As outras reivindicações de exibição são ClaimTypes a serem coletadas do usuário.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Como mencionado, uma reivindicação de exibição com uma referência a um controle de exibição pode executar sua própria validação, por exemplo, verificando o endereço de e-mail. Além disso, a página auto-afirmada suporta o uso de um perfil técnico de validação para validar toda a página, incluindo qualquer entrada do usuário (tipos de reclamação ou controles de exibição), antes de passar para a próxima etapa de orquestração.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Combine o uso de reivindicações de exibição e reivindicações de saída cuidadosamente

Se você especificar um ou mais elementos **do DisplayClaim** em um perfil técnico auto-afirmado, você deve usar um DisplayClaim para *cada* reclamação que deseja exibir na tela e coletar do usuário. Nenhuma reclamação de saída é exibida por um perfil técnico auto-afirmado que contenha pelo menos uma reivindicação de exibição.

Considere o seguinte exemplo `age` no qual uma reivindicação é definida como uma reivindicação **de saída** em uma política base. Antes de adicionar quaisquer alegações de exibição `age` ao perfil técnico auto-afirmado, a reclamação é exibida na tela para coleta de dados do usuário:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Se uma política de folha que herda essa base posteriormente `officeNumber` especificar como uma reivindicação de **exibição:**

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

A `age` reivindicação na política base não é mais apresentada na tela ao usuário - é efetivamente "oculta". Para exibir `age` a reclamação e coletar o valor de `age` idade do usuário, você deve adicionar um **DisplayClaim**.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de reivindicações a serem devolvidas à próxima etapa de orquestração. O **atributo DefaultValue** só entra em vigor se a reclamação nunca tiver sido definida. Se ele foi definido em uma etapa de orquestração anterior, o valor padrão não faz efeito mesmo se o usuário deixar o valor vazio. Para forçar o uso de um valor padrão, defina o atributo **AlwaysUseDefaultValue** como `true`.

Por razões de segurança,`UserInputType` um `Password`valor de solicitação de senha (definido para ) está disponível apenas para os perfis técnicos de validação do perfil técnico auto-afirmado. Você não pode usar a reivindicação de senha nas próximas etapas de orquestração. 

> [!NOTE]
> Nas versões anteriores do IEF (Identity Experience Framework, estrutura de experiência de identidade), as reivindicações de saída foram usadas para coletar dados do usuário. Para coletar dados do usuário, use uma coleção **DisplayClaims.**

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="when-you-should-use-output-claims"></a>Quando você deve usar reivindicações de saída

Em um perfil técnico auto-afirmado, a coleção de sinistros de saída retorna as reivindicações para a próxima etapa de orquestração.

Use reivindicações de saída quando:

- **As reivindicações são saídas por transformação de sinistros de saída**.
- **Definir um valor padrão em uma reclamação de saída** sem coletar dados do usuário ou devolver os dados do perfil técnico de validação. O perfil técnico autodeclarado `LocalAccountSignUpWithLogonEmail` define a declaração **executed-SelfAsserted-Input** como `true`.
- **Um perfil técnico de validação retorna as declarações de saída**: seu perfil técnico pode chamar um perfil técnico de validação que retorna algumas declarações. Talvez você queira juntar as declarações e retorná-las para as próximas etapas de orquestração no percurso do usuário. Por exemplo, ao entrar com uma conta local, o perfil técnico autodeclarado de nome `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação chamado `login-NonInteractive`. Esse perfil técnico valida as credenciais do usuário e também retorna o perfil do usuário. Como 'userPrincipalName', 'displayName', 'givenName' e 'surName'.
- **Um controle de exibição retorna as alegações de saída** - Seu perfil técnico pode ter uma referência a um [controle de exibição](display-controls.md). O controle de exibição retorna algumas reivindicações, como o endereço de e-mail verificado. Talvez você queira juntar as declarações e retorná-las para as próximas etapas de orquestração no percurso do usuário. O recurso de controle de exibição está atualmente em **visualização**.

O exemplo a seguir demonstra o uso de um perfil técnico auto-afirmado que usa tanto alegações de exibição quanto de reivindicações de saída.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Declarações de persistência

O elemento PersistedClaims não é usado. O perfil técnico auto-afirmado não persiste os dados para o Azure AD B2C. Em vez disso, é feita uma chamada para um perfil técnico de validação responsável por persistir os dados. Por exemplo, a política de inscrição usa o perfil técnico autodeclarado `LocalAccountSignUpWithLogonEmail` para coletar o novo perfil do usuário. O perfil técnico `LocalAccountSignUpWithLogonEmail` chama o perfil técnico de validação para criar a conta no Azure AD B2C.

## <a name="validation-technical-profiles"></a>Perfis técnicos de validação

Um perfil técnico de validação é usado para validar algumas ou todas as declarações de saída do perfil técnico de referência. As declarações de entrada do perfil técnico de validação precisam aparecer nas declarações de saída do perfil técnico autodeclarado. O perfil técnico de validação valida a entrada do usuário e pode retornar um erro ao usuário.

O perfil técnico de validação pode ser qualquer perfil técnico na política, como perfis técnicos do [Azure Active Directory](active-directory-technical-profile.md) ou da [API REST](restful-technical-profile.md). No exemplo anterior, o perfil técnico `LocalAccountSignUpWithLogonEmail` valida que o signinName não existe no diretório. Caso contrário, o perfil técnico de validação cria uma conta local e retorna objectId, authenticationSource e newUser. O perfil técnico `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação `login-NonInteractive` para validar as credenciais do usuário.

Também é possível chamar um perfil técnico da API REST com a lógica de negócios, substituir as declarações de entrada ou aprimorar os dados do usuário integrando ainda mais com aplicativos de linha de negócios corporativos. Para saber mais, confira [Perfil técnico de validação](validation-technical-profile.md)

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| configuração.operatingMode <sup>1</sup>| Não | Em uma página de entrada, essa propriedade controla o comportamento do campo nome de usuário, como validação de entradas e mensagens de erro. Valores esperados: `Username` ou `Email`.  |
| Permitir a geração de reclamaçõescom valores anulados| Não| Permitir gerar uma reclamação com valor nulo. Por exemplo, em um caso, o usuário não seleciona uma caixa de seleção.|
| ContentDefinitionReferenceId | Sim | O identificador da [definição de conteúdo](contentdefinitions.md) associada com este perfil técnico. |
| EnforceEmailVerification | Não | Na inscrição ou edição de perfil, reforça a verificação de email. Valores possíveis: `true` (padrão) ou `false`. |
| setting.retryLimit | Não | Controla o número de vezes que um usuário pode tentar fornecer os dados verificados em um perfil técnico de validação. Por exemplo, quanto um usuário tenta se inscrever com uma conta que já existe e continua tentando até alcançar o limite.
| Alvo de inscrição <sup>1</sup>| Não | O identificador de troca do destino da inscrição. Quando o usuário clica no botão de inscrição, o Azure AD B2C executa o identificador de troca especificado. |
| setting.showCancelButton | Não | Mostra o botão cancelar. Valores possíveis: `true` (padrão) ou `false` |
| setting.showContinueButton | Não | Mostra o botão continuar. Valores possíveis: `true` (padrão) ou `false` |
| configuração.showSignupLink <sup>2</sup>| Não | Mostra o botão de inscrição. Valores possíveis: `true` (padrão) ou `false` |
| configuração.forgotPasswordLinkLocalização <sup>2</sup>| Não| Exibe o link de senha esquecido. Valores `AfterInput` possíveis: (padrão) o link é exibido na `None` parte inferior da página ou remove o link de senha esquecido.|
| configuração.enableRememberMe <sup>2</sup>| Não| Exibe o [Keep me assinado na](custom-policy-keep-me-signed-in.md) caixa de seleção. Valores `true` possíveis: `false` , ou (padrão). |
| IncludeClaimResolveingInClaimshandling  | Não | Para reclamações de entrada e saída, especifica se a [resolução de sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores `true`possíveis: `false`  , ou (padrão). Se você quiser usar um resolver sinistros no `true`perfil técnico, defina isso como . |

Observações:
1. Disponível para definição de `unifiedssp`conteúdo `unifiedssd` [DataUri](contentdefinitions.md#datauri) tipo de , ou .
1. Disponível para definição de `unifiedssp`conteúdo `unifiedssd` [DataUri](contentdefinitions.md#datauri) tipo de , ou . [Versão de layout de página](page-layout.md) 1.1.0 e superior.

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** não será usado.
