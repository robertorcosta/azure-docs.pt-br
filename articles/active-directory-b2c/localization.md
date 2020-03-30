---
title: Localização - Diretório Ativo Azure B2C
description: Especifica o elemento Localization de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126763"
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
| ElementType | Sim | Uma referência a um elemento de tipo de declaração ou um elemento de interface do usuário na política. Valores `ClaimType`possíveis: `ErrorMessage` `Predicate`, `UxElement` `GetLocalizedStringsTransformationClaimType`, , ou . O valor `ClaimType` é usado para localizar um dos atributos de declaração, conforme especificado em StringId. O valor `UxElement` é usado para localizar um dos elementos de interface do usuário, conforme especificado em StringId. O valor `ErrorMessage` é usado para localizar uma das mensagens de erro do sistema, conforme especificado em StringId. O valor `Predicate` é usado para localizar uma das mensagens de erro [Predicate](predicates.md), conforme especificado em StringId. O valor `InputValidation` é usado para localizar uma das mensagens de erro do grupo [PredicateValidation](predicates.md), conforme especificado em StringId. O `GetLocalizedStringsTransformationClaimType` valor é usado para copiar strings localizadas em sinistros. Para obter mais informações, consulte [A transformação de reivindicações getLocalizedStringsTransformation](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId | Sim | Se **ElementType** estiver `ClaimType` `Predicate`definido `InputValidation`para , ou , este elemento contém uma referência a um tipo de reivindicação já definido na seção ClaimsSchema. |
| StringId | Sim | Se **ElementType** for definido como `ClaimType`, esse elemento conterá uma referência a um atributo de um tipo de declaração. Valores possíveis: `DisplayName`, `AdminHelpText` ou `PatternHelpText`. O valor `DisplayName` é usado para definir o nome de exibição de declaração. O valor `AdminHelpText` é usado para definir o nome de texto de ajuda do usuário de declaração. O valor `PatternHelpText` é usado para definir o texto de ajuda do padrão de declaração. Se **ElementType** for definido como `UxElement`, esse elemento conterá uma referência a um atributo de um elemento de interface do usuário. Se **ElementType** for definido como `ErrorMessage`, esse elemento especificará o identificador de uma mensagem de erro. Veja [IDs de cadeia de localização](localization-string-ids.md) para obter uma lista completa de identificadores `UxElement`.|


O exemplo a seguir mostra uma página de inscrição localizada. Os três primeiros valores **LocalizedString** definem o atributo de declaração. O terceiro altera o valor do botão continuar. O último deles altera a mensagem de erro.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

O exemplo a seguir mostra um **UserHelpText** localizado de **Predicate** com a ID `IsLengthBetween8And64`. E um **UserHelpText** localizado de **PredicateGroup** com a ID `CharacterClasses` de **PredicateValidation** com a ID `StrongPassword`.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Configurar localização

Este artigo mostra como dar suporte a várias localidades ou idiomas na política de percursos do usuário. Localização exige três etapas: configurar a lista explícita de idiomas com suporte, fornecer coleções e cadeias de caracteres específicas a um idioma e editar ContentDefinition para a página.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Configurar a lista explícita de idiomas com suporte

No elemento **BuildingBlocks**, adicione o elemento **Localization** com a lista de idiomas com suporte. O exemplo a seguir mostra como definir o suporte de localização para inglês (padrão) e espanhol:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Próximas etapas

Veja os seguintes artigos para exemplos de localização:

- [Personalização de idiomas com política personalizada no Azure Active Directory B2C](custom-policy-localization.md)
- [Personalização de idiomas com fluxos de usuário no Azure Active Directory B2C](user-flow-language-customization.md)
