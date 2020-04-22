---
title: Localização - Diretório Ativo Azure B2C
description: Especifica o elemento Localization de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681408"
---
# <a name="localization"></a>Localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **Localization** permite que você dê suporte a várias localidades ou idiomas na política para os percursos do usuário. O suporte de localização em políticas permite que você:

- Configure a lista explícita de idiomas com suporte em uma política e escolha um idioma padrão.
- Forneça coleções e cadeias de caracteres específicas a um idioma.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

O elemento **Localization** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| habilitado | Não | Valores possíveis: `true` ou `false`. |

O elemento **Localization** contém os seguintes elementos XML

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Lista de idiomas com suporte. |
| LocalizedResources | 0:n | Lista de recursos localizados. |

## <a name="supportedlanguages"></a>SupportedLanguages

O elemento **SupportedLanguages** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| DefaultLanguage | Sim | O idioma a ser usado como o padrão para recursos localizados. |
| MergeBehavior | Não | Valores de enumeração de valores mesclados junto com qualquer ClaimType presente em uma política pai com o mesmo identificador. Use esse atributo quando substituir uma declaração especificada na política de base. Valores possíveis: `Append`, `Prepend` ou `ReplaceAll`. O valor `Append` especifica que a coleta de dados presente deve ser acrescentada ao final da coleção especificada na política pai. O valor `Prepend` especifica que a coleta de dados presente deve ser adicionada antes da coleção especificada na política pai. O valor `ReplaceAll` especifica que a coleta de dados definida na política pai deve ser ignorada, em vez disso, usando, em vez disso, os dados definidos na política atual. |

### <a name="supportedlanguages"></a>SupportedLanguages

O elemento **SupportedLanguages** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Exibe o conteúdo que está em conformidade com uma marca de idioma conforme RFC 5646 – Marcas para Identificar Idiomas. |

## <a name="localizedresources"></a>LocalizedResources

O elemento **LocalizedResources** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | Um identificador usado para identificar exclusivamente os recursos localizados. |

O elemento **LocalizedResources** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Define coleções inteiras em várias culturas. Uma coleção pode ter um número diferente de itens e diferentes cadeias de caracteres para várias culturas. Exemplos de coleções incluem enumerações que aparecem em tipos de declaração. Por exemplo, é mostrada uma lista de país/região para o usuário em uma lista suspensa. |
| LocalizedStrings | 0:n | Define todas as cadeias de caracteres, exceto pelas cadeias de caracteres exibidas em coleções, em várias culturas. |

### <a name="localizedcollections"></a>LocalizedCollections

O elemento **LocalizedCollections** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Lista de idiomas com suporte. |

#### <a name="localizedcollection"></a>LocalizedCollection

O elemento **LocalizedCollection** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Faz referência a um elemento ClaimType ou um elemento de interface do usuário no arquivo de política. |
| ElementId | Sim | Uma cadeia de caracteres que contém uma referência a um tipo de declaração já definido na seção ClaimsSchema usada se **ElementType** está definido como um ClaimType. |
| TargetCollection | Sim | A coleção de destino. |

O elemento **LocalizedCollection** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Define uma opção disponível para o usuário selecionar para uma declaração de interface do usuário, como um valor em uma lista suspensa. |

O elemento **Item** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Texto | Sim | A cadeia de caracteres de exibição amigável deve ser mostrada ao usuário na interface do usuário para essa opção. |
| Valor | Sim | O valor da declaração da cadeia de caracteres associada com essa opção. |
| SelectByDefault | Não | Indica se esta opção deve ser selecionada ou não por padrão na interface do usuário. Os valores possíveis: True ou False. |

O exemplo a seguir mostra o uso do elemento **LocalizedCollections**. Ele contém dois elementos **LocalizedCollection**, um para inglês e outro para espanhol. Ambos definem a coleção de **Restrição** da declaração `Gender` com uma lista de itens para inglês e espanhol.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

O elemento **LocalizedStrings** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Uma cadeia de caracteres localizada. |

O elemento **LocalizedString** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Valores possíveis: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Predicate,](#predicate) [InputValidation](#inputvalidation)ou [UxElement](#uxelement).   | 
| ElementId | Sim | Se **ElementType** estiver `ClaimType` `Predicate`definido `InputValidation`para , ou , este elemento contém uma referência a um tipo de reivindicação já definido na seção ClaimsSchema. |
| StringId | Sim | Se **ElementType** for definido como `ClaimType`, esse elemento conterá uma referência a um atributo de um tipo de declaração. Valores possíveis: `DisplayName`, `AdminHelpText` ou `PatternHelpText`. O valor `DisplayName` é usado para definir o nome de exibição de declaração. O valor `AdminHelpText` é usado para definir o nome de texto de ajuda do usuário de declaração. O valor `PatternHelpText` é usado para definir o texto de ajuda do padrão de declaração. Se **ElementType** for definido como `UxElement`, esse elemento conterá uma referência a um atributo de um elemento de interface do usuário. Se **ElementType** for definido como `ErrorMessage`, esse elemento especificará o identificador de uma mensagem de erro. Veja [IDs de cadeia de localização](localization-string-ids.md) para obter uma lista completa de identificadores `UxElement`.|

## <a name="elementtype"></a>ElementType

A referência ElementType a um tipo de reclamação, uma transformação de sinistro ou um elemento de interface de usuário na diretiva a ser localizado.

| Elemento para localizar | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| Nome do provedor de identidade |`ClaimsProvider`| | O ID do elemento ClaimsExchange|
| Atributos do tipo de reivindicação|`ClaimType`|Nome do tipo de reclamação| O atributo da reivindicação de ser localizado. Valores `AdminHelpText`possíveis: `PatternHelpText`, `UserHelpText` `DisplayName`, e .|
|Mensagem de erro|`ErrorMessage`||O ID da mensagem de erro |
|Copia strings localizadas em sinistros|`GetLocalizedStringsTra nsformationClaimType`||O nome da reivindicação de saída|
|Mensagem de usuário predicado|`Predicate`|O nome do predicado| O atributo do predicado a ser localizado. Valores `HelpText`possíveis: .|
|Mensagem de usuário do grupo predicado|`InputValidation`|O ID do elemento PredicateValidation.|O ID do elemento PredicateGroup. O grupo predicado deve ser uma criança do elemento de validação predicada conforme definido no ElementId.|
|Elementos da interface do usuário |`UxElement` | | O ID do elemento interface de usuário a ser localizado.|

## <a name="examples"></a>Exemplos

### <a name="claimsprovider"></a>ClaimsProvider

O valor do Provedor de Sinistros é usado para localizar um dos nomes de exibição dos provedores de sinistros. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

O exemplo a seguir mostra como localizar o nome de exibição dos provedores de sinistros.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

O valor ClaimType é usado para localizar um dos atributos de sinistro. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

O exemplo a seguir mostra como localizar os atributos DisplayName, UserHelpText e PatternHelpText do tipo de solicitação de e-mail.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

O valor ErrorMessage é usado para localizar uma das mensagens de erro do sistema. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

O exemplo a seguir mostra como localizar a mensagem de erro UserMessageIfClaimsPrincipalJáExiste.


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

O valor GetLocalizedStringsTransformationClaimType é usado para copiar strings localizadas em sinistros. Para obter mais informações, consulte [A transformação de reivindicações getLocalizedStringsTransformation](string-transformations.md#getlocalizedstringstransformation)


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

O exemplo a seguir mostra como localizar as reivindicações de saída da transformação de reivindicações GetLocalizedStringsTransformation.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicado

O valor predicado é usado para localizar uma das mensagens de erro [Predicado.](predicates.md) 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

O exemplo a seguir mostra como localizar predicados de ajuda ao texto.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>Validação de entrada

O valor InputValidation é usado para localizar uma das mensagens de erro do grupo [PredicateValidation.](predicates.md) 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

O exemplo a seguir mostra como localizar um texto de ajuda de grupo de validação predicado.

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

O valor UxElement é usado para localizar um dos elementos da interface do usuário. O exemplo a seguir mostra como localizar os botões continuar e cancelar.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Próximas etapas

Veja os seguintes artigos para exemplos de localização:

- [Personalização de idiomas com política personalizada no Azure Active Directory B2C](custom-policy-localization.md)
- [Personalização de idiomas com fluxos de usuário no Azure Active Directory B2C](user-flow-language-customization.md)
