---
title: Configurar a inscrição e a entrada com uma conta do Twitter
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Twitter em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7779730b98630d08af046e7cb402caca1d0c2fe6
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653649"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Twitter usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Criar um aplicativo

Para usar o Twitter como provedor de identidade no Azure AD B2C, você precisará criar um aplicativo do Twitter. Se você ainda não tiver uma conta do Twitter, poderá se inscrever em [https://twitter.com/signup](https://twitter.com/signup) .

1. Entre no site [Desenvolvedores do Twitter](https://developer.twitter.com/en/apps) com suas credencias de conta do Twitter.
1. Selecione  **criar um aplicativo**.
1. Insira um **Nome do aplicativo** e uma **Descrição do aplicativo**.
1. Na **URL do site**, insira `https://your-tenant.b2clogin.com`. Substitua `your-tenant` pelo nome do seu locatário. Por exemplo, `https://contosob2c.b2clogin.com`.
1. Insira `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` como o valor da **URL de Retorno de Chamada**. Substitua `your-tenant` pelo nome do locatário e `your-user-flow-Id` pelo identificador do fluxo de usuário. Por exemplo, `b2c_1A_signup_signin_twitter`. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário e a ID do fluxo do usuário, mesmo que elas estejam definidas com letras maiúsculas no Azure AD B2C.
1. Na parte inferior da página, leia e aceite os termos e, em seguida, selecione **Criar**.
1. Na página **Detalhes do aplicativo**, selecione **Editar > Editar detalhes**, marque a caixa de **Habilitar entrada com o Twitter** e, em seguida, selecione **Salvar**.
1. Selecione **Chaves e tokens** e registre os valores da **Chave da API do consumidor** e da **Chave secreta da API do consumidor** que serão usados mais tarde.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurar o Twitter como um provedor de identidade em seu locatário

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade** e, em seguida, selecione **Twitter**.
1. Insira um **Nome**. Por exemplo, *Twitter*.
1. Para a **ID do cliente**, insira a chave de API do consumidor do aplicativo do Twitter que você criou anteriormente.
1. Para o **segredo do cliente**, insira a chave secreta da API do consumidor que você registrou.
1. Clique em **Salvar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

É necessário armazenar a chave secreta que gravou anteriormente no locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **Nome** para a chave de política. Por exemplo, `TwitterSecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Em **Segredo**, insira o segredo do cliente que você registrou anteriormente.
9. Para **Uso de chave**, selecione `Encryption`.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se você quiser que os usuários entrem usando uma conta do Twitter, defina a conta como um provedor de declarações com o qual o Azure AD B2C pode comunicar-se por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta do Twitter como um provedor de declarações, adicionando-o ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. Substitua o valor de **client_id** pela chave do consumidor que você gravou anteriormente.
5. Salve o arquivo.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com sua conta do Twitter. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade já foi definido, mas não está disponível em nenhuma das telas de inscrição ou de entrada. Para disponibilizá-lo, crie uma duplicata de um percurso do usuário de modelo existente e modifique-o para que ele também tenha o provedor de identidade do Twitter.

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição ou de entrada. Se você adicionar um elemento **ClaimsProviderSelection** à conta do Twitter, um novo botão será exibido quando o usuário chegar à página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada do usuário que você criou.
2. Em **ClaimsProviderSelects**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `TwitterExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação é para o Azure AD B2C comunicar-se com a conta do Twitter para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que você use o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a ID do perfil técnico você já criou. Por exemplo, `Twitter-OAUTH1`.

3. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade do Twitter a um fluxo de usuário 

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário que você deseja para o provedor de identidade do Twitter.
1. Em **provedores de identidade social**, selecione **Twitter**.
1. Clique em **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar fluxo de usuário**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-o para *SignUpSignInTwitter.xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInTwitter`.
1. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_twitter`
1. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignTwitter).
1. Salve suas alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
1. Verifique se o aplicativo Azure AD B2C que você criou está selecionado no campo **Selecionar aplicativo** e teste-o clicando em **Executar agora**.

::: zone-end