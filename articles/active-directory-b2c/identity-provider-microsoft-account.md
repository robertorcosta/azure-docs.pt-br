---
title: Configurar a inscrição e a entrada com uma conta Microsoft
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada aos consumidores com contas Microsoft em seus aplicativos usando o Azure Active Directory B2C.
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
ms.openlocfilehash: a05c987299a8a4967c81b7eb9cc481f253ac5b09
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584843"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta da Microsoft usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="b2c-user-flow"

* [Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem no seu aplicativo.
* Se não tiver feito isso ainda, [adicione um aplicativo de API da Web ao locatário do Azure Active Directory B2C](add-web-api-application.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

* Conclua as etapas em [Introdução às políticas personalizadas no Active Directory B2C](custom-policy-get-started.md).
* Se não tiver feito isso ainda, [adicione um aplicativo de API da Web ao locatário do Azure Active Directory B2C](add-web-api-application.md).

::: zone-end

## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft

Para usar uma conta Microsoft como [provedor de identidade](openid-connect.md) no Azure Active Directory B2C (Azure AD B2C), é preciso criar um aplicativo no locatário do Azure AD. Locatário do Azure AD não é o mesmo que seu locatário do Azure AD B2C. Se ainda não tiver uma conta Microsoft, obtenha uma em [https://www.live.com/](https://www.live.com/).

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure Active Directory escolhendo o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure Active Directory.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Por exemplo, *MSAapp1*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional (Qualquer diretório do Azure AD – Multilocatário) e contas Microsoft pessoais (por exemplo, Skype, Xbox)** .

   Confira mais informações sobre as diferentes seleções de tipo de conta em [Início Rápido: registrar um aplicativo na plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md).
1. Em **URI de redirecionamento (opcional)** , selecione **Web** e insira `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` na caixa de texto. Substitua `<tenant-name>` pelo nome de seu locatário do Azure AD B2C.
1. Escolha **Registrar**
1. Registre a **ID do aplicativo (do cliente)** mostrada na página Visão geral do aplicativo. Ela será necessária durante a configuração do provedor de identidade na próxima seção.
1. Selecione **Certificados e segredos**
1. Clique em **Novo segredo do cliente**
1. Insira uma **Descrição** para o segredo, por exemplo *Senha do aplicativo 1* e clique em **Adicionar**.
1. Registre a senha do aplicativo mostrada na coluna **Valor**. Ela será necessária durante a configuração do provedor de identidade na próxima seção.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurar uma conta da Microsoft como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Provedores de identidade** e depois **Conta Microsoft**.
1. Insira um **Nome**. Por exemplo, *MSA*.
1. Para a **ID do cliente**, insira a ID (do cliente) do aplicativo do Azure AD criado anteriormente.
1. Para o **Segredo do cliente**, insira o segredo que você registrou.
1. Clique em **Salvar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

Se quiser obter as declarações `family_name` e `given_name` do Azure Active Directory, você poderá configurar declarações opcionais para seu aplicativo na interface do usuário do portal do Azure ou no manifesto do aplicativo. Para obter mais informações, veja [Como fornecer declarações opcionais ao aplicativo Azure Active Directory (visualização pública)](../active-directory/develop/active-directory-optional-claims.md).

1. Entre no [portal do Azure](https://portal.azure.com). Pesquise **Azure Active Directory** e selecione-o.
1. Na seção **Gerenciar**, escolha **Registros de aplicativo**.
1. Escolha o aplicativo para o qual você deseja configurar declarações opcionais na lista.
1. Na seção **Gerenciar**, escolha **Configuração de token (versão preliminar)** .
1. Escolha **Adicionar declaração opcional**.
1. Escolha o tipo de token que você deseja configurar.
1. Escolha as declarações opcionais a serem adicionadas.
1. Clique em **Adicionar**.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Agora que você criou o aplicativo em seu locatário do Azure Active Directory, é preciso armazenar o segredo do cliente do aplicativo em seu locatário Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `MSASecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Em **Segredo**, insira o segredo do cliente que você registrou na seção anterior.
1. Para **Uso de chave**, selecione `Signature`.
1. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Para permitir que os usuários entrem usando uma conta Microsoft, defina a conta como um provedor de declarações com o qual o Azure AD B2C pode comunicar-se por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure AD como um provedor de declarações, adicionando o elemento **ClaimsProvider** no arquivo de extensão de sua política.

1. Abra o arquivo de política *TrustFrameworkExtensions.xml*.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Substitua o valor de **client_id** pela *ID do aplicativo (cliente)*  do aplicativo Azure Active Directory que você registrou anteriormente.
1. Salve o arquivo.

Até este ponto, você configurou sua política para que o Azure Active Directory B2C saiba como se comunicar com seu aplicativo com conta Microsoft no Azure Active Directory.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Antes de continuar, carregue a política modificada para verificar se ela não tem nenhum problema até o momento.

1. Navegue até seu locatário do Azure Active Directory B2C no portal do Azure e escolha **Identity Experience Framework**.
1. Na página **Políticas personalizadas**, escolha **Carregar política personalizada**.
1. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
1. Clique em **Carregar**.

Se nenhum erro for exibido no portal, continue para a próxima seção.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, você configurou o provedor de identidade, mas ele ainda não está disponível em nenhuma das telas de inscrição ou entrada. Para disponibilizá-lo, crie uma duplicata de um percurso do usuário existente e, em seguida, mude para que ele também tenha o provedor de identidade da conta Microsoft.

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
1. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
1. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
1. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição ou de entrada. Se você adicionar um elemento **ClaimsProviderSelection** à conta Microsoft, um novo botão será exibido quando o usuário chegar à página.

1. No arquivo *TrustFrameworkExtensions.xml*, localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou.
1. Em **ClaimsProviderSelects**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `MicrosoftAccountExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure Active Directory B2C se comunique com a conta da Microsoft para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
1. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que você use o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para corresponder ao valor `Id` do elemento **TechnicalProfile** do provedor de declarações que você adicionou anteriormente. Por exemplo, `MSA-OIDC`.

1. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade da Microsoft a um fluxo de usuário 

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário que você deseja para o provedor de identidade da Microsoft.
1. Em **provedores de identidade social**, selecione **conta da Microsoft**.
1. Selecione **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar fluxo de usuário**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-a para *SignUpSignInMSA.xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInMSA`.
1. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_msa`
1. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder à ID do percurso do usuário que você criou (SignUpSignInMSA).
1. Salve suas alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
1. Verifique se o aplicativo Azure Active Directory B2C que você criou na seção anterior (ou concluindo os pré-requisitos, por exemplo *webapp1* ou *testapp1*) está selecionado no campo **Selecionar aplicativo** e, em seguida, teste-o clicando em **Executar agora**.
1. Escolha o botão **Conta da Microsoft** e entre.

::: zone-end