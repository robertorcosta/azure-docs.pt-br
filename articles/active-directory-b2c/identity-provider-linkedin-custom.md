---
title: Configure o login com uma conta do LinkedIn usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Configure o login com uma conta do LinkedIn no Azure Active Directory B2C usando políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188164"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada com uma conta do LinkedIn usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como ativar o login para usuários de uma conta do LinkedIn usando [políticas personalizadas](custom-policy-overview.md) no Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md).
- Conta linkedIn - Se você ainda não tiver uma, [crie uma conta](https://www.linkedin.com/start/join).
- Página do LinkedIn - Você precisa de uma página do [LinkedIn](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) para se associar ao aplicativo LinkedIn que você cria na próxima seção.

## <a name="create-an-application"></a>Criar um aplicativo

Para usar o LinkedIn como provedor de identidade no Azure AD B2C, você precisará criar um aplicativo do LinkedIn.

### <a name="create-app"></a>Criar aplicativo

1. Entre no site de [gerenciamento de aplicativo do LinkedIn](https://www.linkedin.com/secure/developer?newapp=) com suas credenciais de conta do LinkedIn.
1. Selecione **Criar aplicativo**.
1. Digite um **nome de aplicativo**.
1. Digite um nome **da empresa** correspondente a um nome de página do LinkedIn. Crie uma página no LinkedIn se você ainda não tiver uma.
1. (Opcional) Digite uma **URL de política de privacidade**. Deve ser uma URL válida, mas não precisa ser um ponto final acessível.
1. Digite um **e-mail de negócios**.
1. Faça upload de uma imagem **do logotipo do App.** A imagem do logotipo deve ser quadrada e suas dimensões devem ser de pelo menos 100x100 pixels.
1. Deixe as configurações padrão na seção **Produtos.**
1. Revisar as informações apresentadas em **termos legais**. Se você concordar com os termos, verifique a caixa.
1. Selecione **Criar aplicativo**.

### <a name="configure-auth"></a>Configurar autenticação

1. Selecione a guia **Auth.**
1. Registre o **ID**do cliente .
1. Revele e grave o Segredo do **Cliente.**
1. Em **configurações OAuth 2.0,** adicione a seguinte **URL redirecionar**. Substitua `your-tenant` pelo nome do seu locatário. Use **todas as letras minúsculas** para o nome do inquilino, mesmo que seja definido com letras maiúsculas no Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que registrou anteriormente no seu locatário do Azure AD B2C.

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **as teclas de diretiva** e **selecione Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **Nome** para a chave de política. Por exemplo, `LinkedInSecret`. O prefixo *B2C_1A_* é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** digite o segredo do cliente que você gravou anteriormente.
9. Para **Uso de chave**, selecione `Signature`.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se você quiser que os usuários entrem usando uma conta do LinkedIn, defina a conta como um provedor de declarações com o qual o Azure AD B2C pode comunicar-se por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Defina uma conta do LinkedIn como um provedor de sinistros adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão de sua política.

1. Abra o arquivo *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** em seu editor. Este arquivo está no [pacote de inicialização de políticas personalizadas][starter-pack] que você baixou como parte de um dos pré-requisitos.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Substitua o valor de **client_id** pelo ID do cliente do aplicativo LinkedIn que você gravou anteriormente.
1. Salve o arquivo.

### <a name="add-the-claims-transformations"></a>Adicione as transformações de sinistros

O perfil técnico do LinkedIn requer que as transformações **de reivindicações do ExtractGivenNameFromLinkedInResponse** **sejam** adicionadas à lista de Transformações de Sinistros. Se você não tiver um elemento **ClaimsTransformations** definido em seu arquivo, adicione os elementos XML pai como mostrado abaixo. As transformações de sinistros também precisam de um novo tipo de reclamação definido chamado **nullStringClaim**.

Adicione o elemento **BuildingBlocks** perto da parte superior do arquivo *TrustFrameworkExtensions.xml.* Consulte *TrustFrameworkBase.xml* para obter um exemplo.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora você tem uma política configurada para que o Azure AD B2C saiba como se comunicar com sua conta do LinkedIn. Tente carregar o arquivo de extensão de sua política para confirmar que ele não tem nenhum problema até agora.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste momento, o provedor de identidade foi configurado, mas não está disponível em nenhuma das telas de inscrição ou login. Para disponibilizá-lo, você criará uma duplicata de um modelo de percurso do usuário existente e, depois, o modificará para que ele também tenha o provedor de identidade do LinkedIn.

1. Abra o arquivo *TrustFrameworkBase.xml* no pacote inicial.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição ou de entrada. Se você adicionar um elemento **ClaimsProviderSelection** à conta do LinkedIn, um novo botão será exibido quando o usuário chegar à página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada do usuário que você criou.
2. Em **ClaimsProviderSelections**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação é para o Azure AD B2C se comunicar com uma conta do LinkedIn para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que você use o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Atualize o valor do **TechnicalProfileReferenceId** para o ID do perfil técnico que você criou anteriormente. Por exemplo, `LinkedIn-OAUTH`.

3. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de um aplicativo que você registra no seu inquilino B2C. Esta seção lista etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-o para *SignUpSignInLinkedIn.xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInLinkedIn`.
3. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignLinkedIn).
5. Salve suas alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se o aplicativo Azure AD B2C que você criou está selecionado no campo **Selecionar aplicativo** e teste-o clicando em **Executar agora**.

## <a name="migration-from-v10-to-v20"></a>Migração de v1.0 para v2.0

O LinkedIn atualizou recentemente [suas APIs de v1.0 para v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Para migrar sua configuração existente para a nova configuração, use as informações nas seções a seguir para atualizar os elementos no perfil técnico.

### <a name="replace-items-in-the-metadata"></a>Substitua itens no Metadados

No elemento **Metadados** existente do **TechnicalProfile,** atualize os seguintes elementos de **item** de:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Para:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Adicionar itens aos Metadados

Nos **Metadados** do **TechnicalProfile,** adicione os seguintes elementos **de item:**

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Atualize as saídasdes

Nas **saídas existentesDo** **Perfil Técnico,** atualize os seguintes elementos **outputClaim** de:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Para:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Adicionar novos elementos OutputClaimsTransformation

Na **SaídaAlegaçõesTransformações** do **Perfil Técnico,** adicione os seguintes elementos **OutputClaimsTransformation:**

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Defina as novas transformações de sinistros e tipo de reclamação

Na última etapa, você adicionou novas transformações de sinistros que precisam ser definidas. Para definir as transformações de sinistros, adicione-as à lista de **Transformações de Sinistros**. Se você não tiver um elemento **ClaimsTransformations** definido em seu arquivo, adicione os elementos XML pai como mostrado abaixo. As transformações de sinistros também precisam de um novo tipo de reclamação definido chamado **nullStringClaim**.

O elemento **BuildingBlocks** deve ser adicionado perto da parte superior do arquivo. Veja o *TrustframeworkBase.xml* como exemplo.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Obtenha um endereço de e-mail

Como parte da migração do LinkedIn de v1.0 para v2.0, uma chamada adicional para outra API é necessária para obter o endereço de e-mail. Se você precisar obter o endereço de e-mail durante a inscrição, faça o seguinte:

1. Complete as etapas acima para permitir que o Azure AD B2C se comfederasse com o LinkedIn para permitir que o usuário faça login. Como parte da federação, o Azure AD B2C recebe o token de acesso para o LinkedIn.
2. Salve o token de acesso do LinkedIn em uma reclamação. [Veja as instruções aqui.](idp-pass-through-custom.md)
3. Adicione o seguinte provedor de reclamações que `/emailAddress` faz a solicitação à API do LinkedIn. Para autorizar essa solicitação, você precisa do token de acesso ao LinkedIn.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Adicione o seguinte passo de orquestração em sua jornada de usuário, para que o provedor de reclamações da API seja acionado quando um usuário faz sinal usando o LinkedIn. Certifique-se de `Order` atualizar o número adequadamente. Adicione este passo imediatamente após a etapa de orquestração que aciona o perfil técnico do LinkedIn.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

A obtenção do endereço de e-mail do LinkedIn durante a inscrição é opcional. Se você optar por não obter o e-mail do LinkedIn, mas exigir um durante a inscrição, o usuário é obrigado a digitar manualmente o endereço de e-mail e validá-lo.

Para obter uma amostra completa de uma diretiva que usa o provedor de identidade do LinkedIn, consulte o [Pacote de inicialização de políticas personalizadas](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
