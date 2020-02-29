---
title: Azure Active Directory B2C de localização
description: Especifica o elemento Localization de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a9fb1d64fb27507e4ee42c39be9ea80fbe5f168
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184436"
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

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| habilitado | Não | Valores possíveis: `true` ou `false`. |

O elemento **Localization** contém os seguintes elementos XML

| Elemento | Ocorrências | DESCRIÇÃO |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Lista de idiomas com suporte. |
| LocalizedResources | 0:n | Lista de recursos localizados. |

## <a name="supportedlanguages"></a>SupportedLanguages

O elemento **SupportedLanguages** contém os seguintes atributos:

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| DefaultLanguage | Sim | O idioma a ser usado como o padrão para recursos localizados. |
| MergeBehavior | Não | Valores de enumeração de valores mesclados junto com qualquer ClaimType presente em uma política pai com o mesmo identificador. Use esse atributo quando substituir uma declaração especificada na política de base. Valores possíveis: `Append`, `Prepend` ou `ReplaceAll`. O valor `Append` especifica que a coleta de dados presente deve ser acrescentada ao final da coleção especificada na política pai. O valor `Prepend` especifica que a coleta de dados presente deve ser adicionada antes da coleção especificada na política pai. O valor `ReplaceAll` especifica que a coleta de dados definida na política pai deve ser ignorada, em vez disso, usando, em vez disso, os dados definidos na política atual. |

### <a name="supportedlanguages"></a>SupportedLanguages

O elemento **SupportedLanguages** contém os seguintes elementos:

| Elemento | Ocorrências | DESCRIÇÃO |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Exibe o conteúdo que está em conformidade com uma marca de idioma conforme RFC 5646 – Marcas para Identificar Idiomas. |

## <a name="localizedresources"></a>LocalizedResources

O elemento **LocalizedResources** contém os seguintes atributos:

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| ID | Sim | Um identificador usado para identificar exclusivamente os recursos localizados. |

O elemento **LocalizedResources** contém os seguintes elementos:

| Elemento | Ocorrências | DESCRIÇÃO |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Define coleções inteiras em várias culturas. Uma coleção pode ter um número diferente de itens e diferentes cadeias de caracteres para várias culturas. Exemplos de coleções incluem enumerações que aparecem em tipos de declaração. Por exemplo, é mostrada uma lista de país/região para o usuário em uma lista suspensa. |
| LocalizedStrings | 0:n | Define todas as cadeias de caracteres, exceto pelas cadeias de caracteres exibidas em coleções, em várias culturas. |

### <a name="localizedcollections"></a>LocalizedCollections

O elemento **LocalizedCollections** contém os seguintes elementos:

| Elemento | Ocorrências | DESCRIÇÃO |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Lista de idiomas com suporte. |

#### <a name="localizedcollection"></a>LocalizedCollection

O elemento **LocalizedCollection** contém os seguintes atributos:

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| ElementType | Sim | Faz referência a um elemento ClaimType ou um elemento de interface do usuário no arquivo de política. |
| ElementId | Sim | Uma cadeia de caracteres que contém uma referência a um tipo de declaração já definido na seção ClaimsSchema usada se **ElementType** está definido como um ClaimType. |
| TargetCollection | Sim | A coleção de destino. |

O elemento **LocalizedCollection** contém os seguintes elementos:

| Elemento | Ocorrências | DESCRIÇÃO |
| ------- | ----------- | ----------- |
| Item | 0:n | Define uma opção disponível para o usuário selecionar para uma declaração de interface do usuário, como um valor em uma lista suspensa. |

O elemento **Item** contém os seguintes atributos:

| Atributo | Obrigatório | DESCRIÇÃO |
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

| Elemento | Ocorrências | DESCRIÇÃO |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Uma cadeia de caracteres localizada. |

O elemento **LocalizedString** contém os seguintes atributos:

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| ElementType | Sim | Uma referência a um elemento de tipo de declaração ou um elemento de interface do usuário na política. Valores possíveis: `ClaimType`, `UxElement`, `ErrorMessage` ou `Predicate`. O valor `ClaimType` é usado para localizar um dos atributos de declaração, conforme especificado em StringId. O valor `UxElement` é usado para localizar um dos elementos de interface do usuário, conforme especificado em StringId. O valor `ErrorMessage` é usado para localizar uma das mensagens de erro do sistema, conforme especificado em StringId. O valor `Predicate` é usado para localizar uma das mensagens de erro [Predicate](predicates.md), conforme especificado em StringId. O valor `InputValidation` é usado para localizar uma das mensagens de erro do grupo [PredicateValidation](predicates.md), conforme especificado em StringId. |
| ElementId | Sim | Se **ElementType** for definido como `ClaimType`, `Predicate` ou `InputValidation`, esse elemento conterá uma referência a um tipo de declaração já definido na seção ClaimsSchema. |
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

### <a name="provide-language-specific-strings-and-collections"></a>Fornecer coleções e cadeias de caracteres específicas a um idioma

Adicione elementos **LocalizedResources** dentro do elemento **Localization** após o encerramento do elemento **SupportedLanguages**. Você adiciona elementos **LocalizedResources** a cada página (definição de conteúdo) e a qualquer idioma ao qual você deseje dar suporte. Para personalizar a página de inscrição ou entrada unificada e páginas de inscrição e MFA (autenticação multifator) para inglês, espanhol e francês, você adiciona os seguintes elementos **LocalizedResources**.

- Página de entrada ou inscrição unificada, inglês `<LocalizedResources Id="api.signuporsignin.en">`
- Página de entrada ou inscrição unificada, espanhol `<LocalizedResources Id="api.signuporsignin.es">`
- Página de entrada ou inscrição unificada, francês `<LocalizedResources Id="api.signuporsignin.fr">`
- Inscrição, inglês `<LocalizedResources Id="api.localaccountsignup.en">`
- Inscrição, espanhol `<LocalizedResources Id="api.localaccountsignup.es">`
- Inscrição, francês `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, inglês `<LocalizedResources Id="api.phonefactor.en">`
- MFA, espanhol `<LocalizedResources Id="api.phonefactor.es">`
- MFA, francês `<LocalizedResources Id="api.phonefactor.fr">`

Cada elemento **LocalizedResources** contém todos os elementos **LocalizedStrings** necessários com vários elementos **LocalizedString** e elementos **LocalizedCollections** com vários elementos **LocalizedCollection**.  O exemplo a seguir adiciona a localização em inglês da página de inscrição:

Observação: este exemplo faz referência aos tipos de declaração `Gender` e `City`. Para usar este exemplo, defina essas declarações. Para obter mais informações, veja [ClaimsSchema](claimsschema.md).

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

A localização da página de inscrição para espanhol.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>Editar a ContentDefinition para a página

Para cada página que você deseja localizar, especifique os códigos de idioma a serem procurados em **ContentDefinition**.

No exemplo a seguir, as cadeias de caracteres personalizadas em inglês (en) e espanhol (es) são adicionadas à página de inscrição. **LocalizedResourcesReferenceId** para cada **LocalizedResourcesReference** é igual à localidade, mas você pode usar qualquer cadeia de caracteres como identificador. Para cada combinação de idioma e página, aponte para os **LocalizedResources** correspondentes criados antes.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

O exemplo a seguir mostra o XML final:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```
