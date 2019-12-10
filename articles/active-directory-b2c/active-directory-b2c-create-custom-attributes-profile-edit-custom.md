---
title: Adicionar seus próprios atributos a políticas personalizadas
titleSuffix: Azure AD B2C
description: Um passo a passo sobre como usar propriedades de extensão e atributos personalizados e incluí-los na interface do usuário.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 92050261777482bae8055f697ef50c2295675c5b
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948770"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Active Directory B2C: usar atributos personalizados em uma política e edição de perfil personalizado

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, você cria um atributo personalizado no diretório Azure Active Directory B2C (Azure AD B2C). Você usará esse novo atributo como uma declaração personalizada na jornada de usuário de edição do perfil.

## <a name="prerequisites"></a>Pré-requisitos

Siga as etapas no artigo [Azure Active Directory B2C: introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Use atributos personalizados para coletar informações sobre seus clientes no Azure Active Directory B2C usando políticas personalizadas
Seu diretório do Microsoft Azure Active Directory B2C é fornecido com um conjunto interno de atributos. Os exemplos são **Nome Fornecido**, **Sobrenome**, **Cidade**, **Código Postal**, e **userPrincipalName**. Geralmente, você precisa criar seus próprios atributos, como esses exemplos:
* Um aplicativo voltado para o cliente precisa manter um atributo, como **LoyaltyNumber.**
* Um provedor de identidade tem um identificador exclusivo do usuário que deve ser salvo como **uniqueUserGUID**.
* Um percurso do usuário personalizado precisa manter o estado do usuário, como **migrationStatus**.

O Azure AD B2C estende o conjunto de atributos armazenados em cada conta de usuário. Você também pode ler e gravar esses atributos usando a [API do Graph do Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

Propriedades de extensão estendem o esquema dos objetos de usuário no diretório. Os termos *propriedade de extensão*, *atributo personalizado* e *declaração personalizada*, se referem à mesma coisa no contexto deste artigo. O nome varia dependendo do contexto, como o aplicativo, objeto ou política.

As propriedades de extensão só podem ser registradas em um objeto do aplicativo, ainda que contenham dados de um usuário. A propriedade é anexada ao aplicativo. O objeto do aplicativo precisa obter acesso de gravação para registrar uma propriedade de extensão. É possível gravar centenas de propriedades de extensão, entre todos os tipos e todos os aplicativos, que podem ser gravados em um único objeto. As propriedades de extensão são adicionadas para o tipo de diretório de destino e tornam-se imediatamente acessíveis no locatário de diretório do Azure Active Directory B2C.
Se o aplicativo for excluído, tanto as propriedades de extensão quanto os dados contidos nelas para todos os usuários serão removidos. Se uma propriedade de extensão for excluída pelo aplicativo, ela será removida nos objetos do directory de destino, e os dados valores serão excluídos.

As propriedades de extensão existem apenas no contexto de um aplicativo registrado no locatário. O id de objeto desse Aplicativo deve ser incluído no **TechnicalProfile** que o usa.

>[!NOTE]
>O diretório do Azure Active Directory B2C geralmente inclui um aplicativo web chamado `b2c-extensions-app`. Este aplicativo é usado principalmente pelas políticas internas B2C para as declarações personalizadas criadas por meio do portal do Microsoft Azure. Recomenda-se que apenas usuários avançados usem registrem extensões para as políticas personalizadas B2C usando este aplicativo.
As instruções para isso são incluídas na seção **Próximas etapas** neste artigo.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Criação de um aplicativo novo para armazenar as propriedades de extensão

1. Abra uma sessão de navegação e navegue até o [portal do Azure](https://portal.azure.com). Entrar com as credenciais administrativas do diretório do B2C que você deseja configurar.
2. Selecione **Azure Active Directory** no menu de navegação à esquerda. Talvez seja necessário localizá-lo, para isso, selecione **Mais serviços**.
3. Selecione **Registros do Aplicativo**. Selecione **Novo registro de aplicativo**.
4. Forneça as seguintes entradas:
    * um nome para o aplicativo Web: **WebApp-GraphAPI-DirectoryExtensions**.
    * Tipo de aplicativo: **Web app/API**.
    * A URL de logon: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Clique em **Criar**.
6. Selecione o aplicativo web criado recentemente.
7. Selecione **as configurações** > **Permissões necessárias**.
8. Selecione a API **Azure Active Directory do Windows**.
9. Insira uma marca de seleção em Permissões de Aplicativo: **Ler e gravar dados do diretório**. Em seguida, selecione **Salvar**.
10. Escolha **Conceder permissões** e confirme **Sim**.
11. Copiar os identificadores a seguir para a sua área de transferência e salve-os:
    * **ID do aplicativo**. Exemplo: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **ID de objeto**. Exemplo: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Modifique a política personalizada para adicionar o **ApplicationObjectId**

Ao seguir as etapas em [Azure Active Directory B2C: Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md), você baixou e modificou os [arquivos de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nomeados **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml**, e **PasswordReset.xml**. Nesta etapa, você fará mais modificações a esses arquivos.

* Abra o arquivo **Trustframeworkbase** e adicione a seção `Metadata` conforme mostrado no exemplo a seguir. Insira a ID de objeto que você registrou anteriormente para o valor `ApplicationObjectId` e a ID do aplicativo que você registrou para o valor `ClientId`:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> Às vezes, quando o **TechnicalProfile** grava pela primeira vez para a propriedade de extensão recém-criada, pode ocorrer um erro ocasional. A propriedade de extensão é criada na primeira vez que ela é usada.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Usar a nova propriedade de extensão / atributo personalizado em um percurso do usuário

1. Abra o arquivo **Profileedit**.
2. Adicione uma declaração personalizada `loyaltyId`. Ao incluir declaração personalizada no elemento `<RelyingParty>`, está incluído no token para o aplicativo.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Abra o arquivo **TrustFrameworkExtensions.xml** e adicione o elemento `<ClaimsSchema>` e seus elementos filhos dentro do elemento `BuildingBlocks`:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Adicionar a mesma definição `ClaimType` para **Trustframeworkbase**. Não é necessário adicionar uma `ClaimType` definição na base e os arquivos de extensões. No entanto, as próximas etapas adicionam o `extension_loyaltyId` a **TechnicalProfiles** no arquivo de base. Portanto, o validador de política rejeitará o upload do arquivo base sem ele. Poderá ser útil rastrear a execução do percurso do usuário chamado **ProfileEdit** no arquivo **TrustFrameworkBase.xml**. Pesquise o percurso do usuário com o mesmo nome em seu editor. Pesquise o percurso do usuário do mesmo nome no seu editor e observe que a orquestração da etapa 5 invoca o **TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate**. Pesquise e inspecione esse **TechnicalProfile** para se familiarizar com o fluxo.

5. Abra o arquivo **Trustframeworkbase** e adicione `loyaltyId` como declaração de entrada e saída no **TechnicalProfile SelfAsserted-ProfileUpdate**:

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. No arquivo **Trustframeworkbase**, adicione a `loyaltyId` declaração **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. Essa adição persiste o valor da declaração na propriedade de extensão para o usuário atual no diretório:

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. No arquivo **TrustFrameworkBase.xml**, adicione a `loyaltyId` declaração **TechnicalProfile AAD-UserReadUsingObjectId** para ler o valor do atributo de extensão sempre que um usuário fizer logon. Até o momento, os **TechnicalProfiles** só foram alterados no fluxo de contas locais. Se quiser o novo atributo no fluxo de uma conta social/federada, será necessário alterar um conjunto diferente de **TechnicalProfiles**. Consulte a seção **Próximas Etapas**.

    ```xml
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
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Abra a Folha Azure Active Directory B2C e navegue até **Identity Experience Framework** >  **Políticas personalizadas**.
1. Selecione a política personalizada que você carregou. Selecione **Executar Agora**.
1. Criar conta usando um endereço de email.

O token de id enviado novamente para o seu aplicativo inclui a propriedade de extensão nova como uma declaração personalizada precedida por **extension_loyaltyId**. Veja os exemplos a seguir:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Próximos passos

1. Adicione a nova declaração aos fluxos para logons de conta social alterando os **TechnicalProfiles** listados. Contas sociais e federadas usam estes dois **TechnicalProfiles** para entrar. Gravam e leem dados de usuário usando o **alternativeSecurityId** como o localizador do objeto de usuário.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Use os mesmos atributos de extensão entre políticas internas e personalizadas. Quando você adiciona atributos de tensão ou personalizados, por meio da experiência do portal, esses atributos são registrados usando o **b2c-extensions-app** que existe em cada locatário B2C. Para realizar as etapas a seguir para usar os atributos de extensão em sua política personalizada:

   a. Em seu locatário B2C no portal.azure.com, navegue até **Azure Active Directory** e selecione **Registros de aplicativo**.
   b. Localize seu **b2c-extensions-app** e selecione-o.
   c. Em **Essentials**, registre a **ID de Aplicativo** e a **ID de objeto**.
   d. Inclua-os em seus metadados de perfil técnico comuns do **AAD** dessa maneira:

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. Mantenha a consistência com a experiência do portal. Crie esses atributos usando a interface do usuário do portal antes de usá-los em suas políticas personalizadas. Quando você cria um atributo **ActivationStatus** no portal, é necessário fazer referência a ele da seguinte maneira:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Referência

Para obter mais informações sobre as propriedades de extensão, consulte o artigo [Extensões de esquema de diretório | Conceitos da API do Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * Um **TechnicalProfile** é um tipo de elemento, ou uma função, que define o nome de um ponto de extremidade, metadados e protocolo. O **TechnicalProfile** detalha a troca de declarações que executa o Identity Experience Framework. Quando essa função é chamada em uma etapa de orquestração ou de outro **TechnicalProfile**, o **InputClaims** e o **OutputClaims** são fornecidos como parâmetros pelo chamador.
> * Atributos de extensão na API do Graph são nomeados usando a convenção `extension_ApplicationObjectID_attributename`.
> * Políticas personalizadas se referem aos atributos de extensão como **extension_attributename**. Esta referência omite a **ApplicationObjectId** em XML.
> * Você precisa especificar a ID do atributo no seguinte formato **extension_attributename** onde quer que esteja sendo referenciado.
