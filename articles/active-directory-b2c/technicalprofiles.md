---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Especifique o elemento TechnicalProfiles de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3275e31744faba5b029e5a4619a51420400b9d0a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425602"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um elemento **TechnicalProfiles** contém um conjunto de perfis técnicos com suporte no provedor de declarações. Cada provedor de declarações precisa ter um ou mais perfis técnicos que determinam os pontos de extremidade e os protocolos necessários para a comunicação com o provedor de declarações. Um provedor de declarações pode ter vários perfis técnicos.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

O elemento **TechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Description |
|---------|---------|---------|
| ID | Sim | Um identificador exclusivo do perfil técnico. O perfil técnico pode ser referenciado usando esse identificador em outros elementos no arquivo de política. Por exemplo, **OrchestrationSteps** e **ValidationTechnicalProfile**. |

O **TechnicalProfile** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Domínio | 0:1 | O nome de domínio do perfil técnico. Por exemplo, se o perfil técnico especificar o provedor de identidade Facebook, o nome de domínio será Facebook.com. |
| DisplayName | 0:1 | O nome do perfil técnico que pode ser exibido aos usuários. |
| Description | 0:1 | A descrição do perfil técnico que pode ser exibida aos usuários. |
| Protocolo | 0:1 | O protocolo usado para a comunicação com a outra parte. |
| Metadados | 0:1 | Uma coleção de pares chave-valor que são usados pelo protocolo para a comunicação com o ponto de extremidade no decorrer de uma transação. |
| InputTokenFormat | 0:1 | O formato do token de entrada. Valores possíveis: `JSON`, `JWT`, `SAML11` ou `SAML2`. O valor `JWT` representa um Token Web JSON de acordo com a especificação IETF. O valor `SAML11` representa um token de segurança SAML 1.1 de acordo com a especificação OASIS.  O valor `SAML2` representa um token de segurança SAML 2.0 de acordo com a especificação OASIS. |
| OutputTokenFormat | 0:1 | O formato do token de saída. Valores possíveis: `JSON`, `JWT`, `SAML11` ou `SAML2`. |
| CryptographicKeys | 0:1 | Uma lista de chaves de criptografia que são usadas no perfil técnico. |
| InputClaimsTransformations | 0:1 | Uma lista de referências definidas previamente a transformações de declarações que devem ser executadas antes que uma declaração seja enviada ao provedor de declarações ou à terceira parte confiável. |
| InputClaims | 0:1 | Uma lista de referências definidas previamente aos tipos de declaração que são usados como entrada no perfil técnico. |
| PersistedClaims | 0:1 | Uma lista de referências definidas previamente aos tipos de declaração que persistem no provedor de declarações relacionado ao perfil técnico. |
| DisplayClaims | 0:1 | Uma lista das referências definidas anteriormente para os tipos de declaração que são apresentados pelo provedor de declarações relacionado ao [perfil técnico autodeclarado](self-asserted-technical-profile.md). O recurso DisplayClaims está atualmente em **Visualização**. |
| OutputClaims | 0:1 | Uma lista de referências definidas previamente aos tipos de declaração que são usados como saída no perfil técnico. |
| OutputClaimsTransformations | 0:1 | Uma lista de referências definidas previamente a transformações de declarações que devem ser executadas depois que as declarações são recebidas do provedor de declarações. |
| ValidationTechnicalProfiles | 0:n | Uma lista de referências a outros perfis técnicos que o perfil técnico usa para fins de validação. Para obter mais informações, confira [perfil técnico de validação](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Controla a produção do nome da entidade em tokens, em que o nome da entidade é especificado separadamente das declarações. Por exemplo, OAuth ou SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | Um identificador de um perfil técnico do qual todas as declarações de entrada e saídas devem ser adicionadas a esse perfil técnico. O perfil técnico referenciado precisa ser definido no mesmo arquivo de política. |
| IncludeTechnicalProfile |0:1 | Um identificador de um perfil técnico do qual todos os dados devem ser adicionados a esse perfil técnico. O perfil técnico referenciado precisa existir no mesmo arquivo de política. |
| UseTechnicalProfileForSessionManagement | 0:1 | Um perfil técnico diferente a ser usado para gerenciamento de sessão. |
|EnabledForUserJourneys| 0:1 |Controla se o perfil técnico é executado em um percurso do usuário.  |

## <a name="protocol"></a>Protocolo

O elemento **Protocol** contém os seguintes atributos:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| Nome | Sim | O nome de um protocolo válido com suporte no Azure AD B2C que é usado como parte do perfil técnico. Valores possíveis: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `Proprietary`, `session management`, `self-asserted`ou `None`. |
| Manipulador | Não | Quando o nome do protocolo for definido como `Proprietary`, especifique o nome totalmente qualificado do assembly usado pelo Azure AD B2C para determinar o manipulador de protocolo. |

## <a name="metadata"></a>Metadados

Um elemento **Metadata** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Item | 0:n | Os metadados que se relacionam ao perfil técnico. Cada tipo de perfil técnico tem um conjunto diferente de itens de metadados. Confira a seção de tipos de perfil técnico para obter mais informações. |

### <a name="item"></a>Item

O elemento **Item** do elemento **Metadata** contém os seguintes atributos:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| Chave | Sim | A chave de metadados. Confira cada tipo de perfil técnico para obter a lista de itens de metadados. |

## <a name="cryptographickeys"></a>CryptographicKeys

O elemento **CryptographicKeys** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Chave | 1:n | Uma chave de criptografia usada neste perfil técnico. |

### <a name="key"></a>Chave

O elemento **Key** contém o seguinte atributo:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ID | Não | Um identificador exclusivo de um par de chaves específico referenciado por outros elementos no arquivo de política. |
| StorageReferenceId | Sim | Um identificador de um contêiner de chave de armazenamento referenciado por outros elementos no arquivo de política. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

O elemento **InputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | O identificador de uma transformação de declarações que deve ser executado antes que uma declaração seja enviada ao provedor de declarações ou à terceira parte confiável. Uma transformação de declarações pode ser usada para modificar as declarações de ClaimsSchema existentes ou gerar novas declarações. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

O elemento **InputClaimsTransformation** contém o seguinte atributo:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de declarações já definido no arquivo de política ou no arquivo de política pai. |

## <a name="inputclaims"></a>InputClaims

O elemento **InputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Um tipo de declaração de entrada esperado. |

### <a name="inputclaim"></a>InputClaim

O elemento **InputClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de declaração já definido na seção ClaimsSchema no arquivo de política ou no arquivo de política pai. |
| DefaultValue | Não | Um valor padrão a ser usado para criar uma declaração se a declaração indicada por ClaimTypeReferenceId não existir. Assim, a declaração resultante poderá ser usada como um InputClaim pelo perfil técnico. |
| PartnerClaimType | Não | O identificador do tipo de declaração do parceiro externo para o qual o tipo de declaração da política especificado é mapeado. Se o atributo PartnerClaimType não for especificado, o tipo de declaração de política especificado será mapeado para o tipo de declaração de parceiro com o mesmo nome. Use essa propriedade quando seu nome do tipo de declaração for diferente do da outra parte. Por exemplo, o primeira nome da declaração é 'givenName', enquanto o parceiro usa uma declaração chamada 'first_name'. |

## <a name="displayclaims"></a>DisplayClaims

O elemento **DisplayClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | Um tipo de declaração de entrada esperado. |

O recurso DislayClaims está atualmente em **Visualização**.

### <a name="displayclaim"></a>DisplayClaim

O elemento **DisplayClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Não | O identificador de um tipo de declaração já definido na seção ClaimsSchema no arquivo de política ou no arquivo de política pai. |
| DisplayControlReferenceId | Não | O identificador de um [controle de exibição](display-controls.md) já definido na seção de ClaimsSchema no arquivo de política ou de política pai. |
| Obrigatório | Não | Indica se a declaração de exibição é necessária. |

O **DisplayClaim** requer que você especifique um `ClaimTypeReferenceId` ou `DisplayControlReferenceId`.

### <a name="persistedclaims"></a>PersistedClaims

O elemento **PersistedClaims** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | O tipo de declaração que deverá persistir. |

### <a name="persistedclaim"></a>PersistedClaim

O elemento **PersistedClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de declaração já definido na seção ClaimsSchema no arquivo de política ou no arquivo de política pai. |
| DefaultValue | Não | Um valor padrão a ser usado para criar uma declaração se a declaração indicada por ClaimTypeReferenceId não existir. Assim, a declaração resultante poderá ser usada como um InputClaim pelo perfil técnico. |
| PartnerClaimType | Não | O identificador do tipo de declaração do parceiro externo para o qual o tipo de declaração da política especificado é mapeado. Se o atributo PartnerClaimType não for especificado, o tipo de declaração de política especificado será mapeado para o tipo de declaração de parceiro com o mesmo nome. Use essa propriedade quando seu nome do tipo de declaração for diferente do da outra parte. Por exemplo, o primeira nome da declaração é 'givenName', enquanto o parceiro usa uma declaração chamada 'first_name'. |

## <a name="outputclaims"></a>OutputClaims

O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Um tipo de declaração de saída esperado. |

### <a name="outputclaim"></a>OutputClaim

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de declaração já definido na seção ClaimsSchema no arquivo de política ou no arquivo de política pai. |
| DefaultValue | Não | Um valor padrão a ser usado para criar uma declaração se a declaração indicada por ClaimTypeReferenceId não existir. Assim, a declaração resultante poderá ser usada como um InputClaim pelo perfil técnico. |
|AlwaysUseDefaultValue |Não |Força o uso do valor padrão.  |
| PartnerClaimType | Não | O identificador do tipo de declaração do parceiro externo para o qual o tipo de declaração da política especificado é mapeado. Se o atributo PartnerClaimType não for especificado, o tipo de declaração de política especificado será mapeado para o tipo de declaração de parceiro com o mesmo nome. Use essa propriedade quando seu nome do tipo de declaração for diferente do da outra parte. Por exemplo, o primeira nome da declaração é 'givenName', enquanto o parceiro usa uma declaração chamada 'first_name'. |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

O elemento **OutputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Os identificadores de transformações de declarações que devem ser executados antes que uma declaração seja enviada ao provedor de declarações ou à terceira parte confiável. Uma transformação de declarações pode ser usada para modificar as declarações de ClaimsSchema existentes ou gerar novas declarações. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

O elemento **OutputClaimsTransformation** contém o seguinte atributo:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de declarações já definido no arquivo de política ou no arquivo de política pai. |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

O elemento **ValidationTechnicalProfiles** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Os identificadores dos perfis técnicos usados validam algumas ou todas as declarações de saída do perfil técnico de referência. Todas as declarações de entrada do perfil técnico referenciado precisam aparecer nas declarações de saída do perfil técnico de referência. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

O elemento **ValidationTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no arquivo de política ou no arquivo de política pai. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

O **SubjectNamingInfo** contém o seguinte atributo:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ClaimType | Sim | Um identificador de um tipo de declaração já definido na seção ClaimsSchema no arquivo de política. |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

O elemento **IncludeTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no arquivo de política ou no arquivo de política pai. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

O elemento **UseTechnicalProfileForSessionManagement** contém o seguinte atributo:

| Atributo | Obrigatório | Description |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no arquivo de política ou no arquivo de política pai. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

O **ClaimsProviderSelections** em um percurso do usuário define a lista de opções de seleção de provedor de declarações e sua ordem. Com o elemento **EnabledForUserJourneys**, você filtra qual provedor de declarações está disponível para o usuário. O elemento **EnabledForUserJourneys** contém um dos seguintes valores:

- **Always**, executar o perfil técnico.
- **Never**, ignorar o perfil técnico.
- **OnClaimsExistence** executar apenas quando existir uma determinado declaração, especificada no perfil técnico.
- **OnItemExistenceInStringCollectionClaim**, executar apenas quando existir um item em uma declaração de coleção de cadeia de caracteres.
- **OnItemAbsenceInStringCollectionClaim** executar somente quando um item não existir em uma declaração de coleção de cadeia de caracteres.

O uso de **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** ou **OnItemAbsenceInStringCollectionClaim**, exige que você forneça os seguintes metadados: **ClaimTypeOnWhichToEnable** especifica o tipo da declaração a ser avaliada, **ClaimValueOnWhichToEnable** especifica o valor a ser comparado.

O seguinte perfil técnico é executado somente quando a coleção de cadeia de caracteres **identityProviders** contém o valor de `facebook.com`:

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
