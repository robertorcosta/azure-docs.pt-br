---
title: Adicionar seus próprios atributos a políticas personalizadas
titleSuffix: Azure AD B2C
description: Um passo a passo sobre como usar propriedades de extensão e atributos personalizados e incluí-los na interface do usuário.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dbeb2540084fad2cfab3ce360dd15b60a75e5e59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389319"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: habilitar atributos personalizados em uma política de perfil Personalizada

No artigo [adicionar declarações e personalizar a entrada do usuário usando políticas personalizadas](custom-policy-configure-user-input.md) , você aprende a usar os [atributos de perfil do usuário](user-profile-attributes.md)interno. Neste artigo, você habilita um atributo personalizado em seu diretório Azure Active Directory B2C (Azure AD B2C). Posteriormente, você pode usar o novo atributo como uma declaração personalizada em [fluxos de usuário](user-flow-overview.md) ou [políticas personalizadas](custom-policy-get-started.md) simultaneamente.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos

Siga as etapas no artigo [Azure Active Directory B2C: introdução às políticas personalizadas](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Usar atributos personalizados para coletar informações sobre seus clientes 

Seu diretório de Azure AD B2C é fornecido com um [conjunto interno de atributos](user-profile-attributes.md). No entanto, geralmente você precisa criar seus próprios atributos para gerenciar seu cenário específico, por exemplo, quando:

* Um aplicativo voltado para o cliente precisa manter um atributo de **fidelidadeid** .
* Um provedor de identidade tem um identificador de usuário exclusivo, **uniqueUserGUID**, que deve ser persistido.
* Uma jornada de usuário personalizado precisa manter o estado do usuário, **migrationStatus**, para que outra lógica opere.

Azure AD B2C permite que você estenda o conjunto de atributos armazenados em cada conta de usuário. Você também pode ler e gravar esses atributos usando a [API do Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>Aplicativo de extensões de Azure AD B2C

Atributos de extensão só podem ser registrados em um objeto de aplicativo, mesmo que possam conter dados para um usuário. O atributo de extensão é anexado ao aplicativo chamado b2c-extensions-app. Não modifique esse aplicativo, pois ele é usado pelo Azure AD B2C para armazenar dados do usuário. Você pode encontrar esse aplicativo em Azure AD B2C, registros de aplicativo.

Os termos *propriedade de extensão*, *atributo personalizado* e *declaração personalizada*, se referem à mesma coisa no contexto deste artigo. O nome varia dependendo do contexto, como o aplicativo, objeto ou política.

## <a name="get-the-application-properties"></a>Obter as propriedades do aplicativo

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **registros de aplicativo**e, em seguida, selecione **todos os aplicativos**.
1. Selecione o `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` aplicativo.
1. Copiar os identificadores a seguir para a sua área de transferência e salve-os:
    * **ID do aplicativo**. Exemplo: `11111111-1111-1111-1111-111111111111`.
    * **ID do objeto**. Exemplo: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Modificar sua política personalizada

Para habilitar atributos personalizados em sua política, forneça a **ID do aplicativo** e a ID de **objeto** do aplicativo nos metadados do perfil técnico comum do AAD. O perfil técnico do *AAD-comum* é encontrado na base [Azure Active Directory](active-directory-technical-profile.md) perfil técnico e fornece suporte para o gerenciamento de usuários do Azure AD. Outros perfis técnicos do Azure AD incluem o AAD-Common para aproveitar sua configuração. Substitua o perfil técnico do AAD-comum no arquivo de extensão.

1. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Localize o elemento ClaimsProviders. Adicione um novo Claimprovider ao elemento ClaimsProviders.
1. Substitua `ApplicationObjectId` pela ID de objeto que você registrou anteriormente. Em seguida, substitua `ClientId` pela ID do aplicativo que você registrou anteriormente no trecho abaixo.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Carregar sua política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário de Azure ad B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Selecione **carregar política personalizada**e, em seguida, carregue os arquivos de política de TrustFrameworkExtensions.xml que você alterou.

> [!NOTE]
> Na primeira vez que o perfil técnico do Azure AD persistir a declaração para o diretório, ele verificará se o atributo personalizado existe. Caso contrário, ele cria o atributo personalizado.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Criar um atributo personalizado por meio de portal do Azure

Os mesmos atributos de extensão são compartilhados entre políticas internas e personalizadas. Quando você adiciona atributos personalizados por meio da experiência do portal, esses atributos são registrados usando o **B2C-Extensions-app** que existe em todos os locatários do B2C.

Você pode criar esses atributos usando a interface do usuário do portal antes ou depois de usá-los em suas políticas personalizadas. Siga as orientações sobre como [definir atributos personalizados no Azure Active Directory B2C](user-flow-custom-attributes.md). Ao criar um atributo **lealdadeid** no portal, você deve consultá-lo da seguinte maneira:

|Nome     |Usado em |
|---------|---------|
|`extension_loyaltyId`  | Política personalizada|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [API do Microsoft Graph](manage-user-accounts-graph-api.md)|

O exemplo a seguir demonstra o uso de atributos personalizados em uma definição de declaração de política personalizada Azure AD B2C.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

O exemplo a seguir demonstra o uso de um atributo personalizado em Azure AD B2C política personalizada em um perfil técnico, entrada, saída e declarações persistentes.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Usar um atributo personalizado em uma política

Siga as orientações sobre como [adicionar declarações e personalizar a entrada do usuário usando políticas personalizadas](custom-policy-configure-user-input.md). Este exemplo usa uma declaração interna "City". Para usar um atributo personalizado, substitua ' City ' por seus próprios atributos personalizados.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

- [Azure AD B2C atributos de perfil de usuário](user-profile-attributes.md)
- [Definição de atributos de extensão](user-profile-attributes.md#extension-attributes)
- [Gerenciar Azure AD B2C contas de usuário com Microsoft Graph](manage-user-accounts-graph-api.md)
