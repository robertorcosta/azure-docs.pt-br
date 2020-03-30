---
title: Adicione seus próprios atributos às políticas personalizadas
titleSuffix: Azure AD B2C
description: Um passo a passo sobre como usar propriedades de extensão e atributos personalizados e incluí-los na interface do usuário.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc5204518cb6e801ba661aecd5498a501122225f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473652"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: Habilite atributos personalizados em uma política de perfil personalizado

No [Adicionar reivindicações e personalizar](custom-policy-configure-user-input.md) a entrada do usuário usando o artigo de políticas personalizadas, você aprende a usar [atributos](user-profile-attributes.md)de perfil de usuário incorporados . Neste artigo, você habilita um atributo personalizado no diretório ativo do Azure B2C (Azure AD B2C). Mais tarde, você pode usar o novo atributo como uma reivindicação personalizada em [fluxos de usuário](user-flow-overview.md) ou [políticas personalizadas](custom-policy-get-started.md) simultaneamente.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos

Siga as etapas no artigo [Azure Active Directory B2C: introdução às políticas personalizadas](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Use atributos personalizados para coletar informações sobre seus clientes 

Seu diretório Azure AD B2C vem com um [conjunto integrado de atributos](user-profile-attributes.md). No entanto, muitas vezes você precisa criar seus próprios atributos para gerenciar seu cenário específico, por exemplo, quando:

* Um aplicativo voltado para o cliente precisa persistir um atributo **LoyaltyId.**
* Um provedor de identidade tem um identificador de usuário único, **exclusivoUserGUID,** que deve ser persistido.
* Uma jornada personalizada do usuário precisa persistir o estado do usuário, **migrationStatus**, para que outra lógica seja operada.

O Azure AD B2C permite estender o conjunto de atributos armazenados em cada conta de usuário. Você também pode ler e escrever esses atributos usando a [API](manage-user-accounts-graph-api.md)do Microsoft Graph .

## <a name="azure-ad-b2c-extensions-app"></a>Aplicativo de extensões Azure AD B2C

Os atributos de extensão só podem ser registrados em um objeto de aplicativo, mesmo que eles possam conter dados para um usuário. O atributo de extensão é anexado ao aplicativo chamado b2c-extensions-app. Não modifique este aplicativo, pois ele é usado pelo Azure AD B2C para armazenar dados do usuário. Você pode encontrar este aplicativo em Azure AD B2C, registros de aplicativos.

Os termos *propriedade de extensão*, *atributo personalizado* e *declaração personalizada*, se referem à mesma coisa no contexto deste artigo. O nome varia dependendo do contexto, como o aplicativo, objeto ou política.

## <a name="get-the-application-properties"></a>Obtenha as propriedades do aplicativo

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **inscrições de aplicativos (Preview)** e selecione **Todos os aplicativos**.
1. Selecione `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` a aplicação.
1. Copiar os identificadores a seguir para a sua área de transferência e salve-os:
    * **ID de aplicação**. Exemplo: `11111111-1111-1111-1111-111111111111`.
    * **ID do objeto .** Exemplo: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Modifique sua política personalizada

Para habilitar atributos personalizados em sua política, forneça **ID de aplicativo** e **ID de objeto** de aplicativo nos metadados de perfil técnico AAD-Common. O perfil técnico *AAD-Common* é encontrado no perfil técnico do [Azure Active Directory](active-directory-technical-profile.md) base e fornece suporte para o gerenciamento de usuários do Azure AD. Outros perfis técnicos do Azure AD incluem o AAD-Common para alavancar sua configuração. Substituir o perfil técnico AAD-Common no arquivo de extensão.

1. Abra o arquivo de extensões da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Localize o elemento ClaimsProviders. Adicione um novo Provedor de Sinistros ao elemento Provedores de Sinistros.
1. Substitua pelo `ApplicationObjectId` ID do objeto que você gravou anteriormente. Em `ClientId` seguida, substitua-o pelo ID de aplicativo que você gravou anteriormente no trecho abaixo.

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
      <TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Faça upload de sua política personalizada

1. Faça login no [portal Azure](https://portal.azure.com).
2. Certifique-se de que está usando o diretório que contém o inquilino Azure AD selecionando o filtro **de assinatura Diretório +** no menu superior e escolhendo o diretório que contém o inquilino Azure AD B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Selecione **'Enviar diretiva personalizada'** e, em seguida, faça upload dos arquivos de diretiva TrustFrameworkExtensions.xml que você alterou.

> [!NOTE]
> A primeira vez que o perfil técnico do Azure AD persiste na reivindicação ao diretório, ele verifica se o atributo personalizado existe. Se não, cria o atributo personalizado.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Crie um atributo personalizado através do portal Azure

Os mesmos atributos de extensão são compartilhados entre políticas incorporadas e personalizadas. Quando você adiciona atributos personalizados através da experiência do portal, esses atributos são registrados usando o **aplicativo b2c-extensions-app** que existe em cada inquilino B2C.

Você pode criar esses atributos usando a ui do portal antes ou depois de usá-los em suas políticas personalizadas. Siga a orientação de como [definir atributos personalizados no Azure Active Directory B2C](user-flow-custom-attributes.md). Quando você cria um atributo **loyaltyId** no portal, você deve se referir a ele da seguinte forma:

|Nome     |Usado em |
|---------|---------|
|`extension_loyaltyId`  | Política personalizada|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [API do Microsoft Graph](manage-user-accounts-graph-api.md)|

O exemplo a seguir demonstra o uso de atributos personalizados em uma definição de solicitação de política personalizada Azure AD B2C.

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

O exemplo a seguir demonstra o uso de um atributo personalizado na política personalizada Azure AD B2C em um perfil técnico, entrada, saída e reivindicações persistidas.

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

## <a name="use-a-custom-attribute-in-a-policy"></a>Use um atributo personalizado em uma política

Siga as orientações de como adicionar sinistros e personalizar a [entrada do usuário usando políticas personalizadas.](custom-policy-configure-user-input.md) Esta amostra usa uma reivindicação incorporada "cidade". Para usar um atributo personalizado, substitua 'cidade' por seus próprios atributos personalizados.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

- [Atributos de perfil de usuário Azure AD B2C](user-profile-attributes.md)
- [Definição de atributos de extensão](user-profile-attributes.md#extension-attributes)
- [Gerenciar contas de usuários Azure AD B2C com o Microsoft Graph](manage-user-accounts-graph-api.md)
