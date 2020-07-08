---
title: Configurar a entrada com uma conta da Microsoft usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como usar políticas personalizadas para habilitar a MSA (conta da Microsoft) como um provedor de identidade usando o protocolo OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: db81f8b60cf4883223f6fc084c19c8da1d07bc9a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388095"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada com uma conta Microsoft usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma conta Microsoft usando as [políticas personalizadas](custom-policy-overview.md) no Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md).
- Se você ainda não tiver uma conta Microsoft, crie uma em [https://www.live.com/](https://www.live.com/).

## <a name="register-an-application"></a>Registrar um aplicativo

Para habilitar a entrada de usuários com um conta Microsoft, é preciso registrar um aplicativo no locatário do Azure Active Directory. Locatário do Azure AD não é o mesmo que seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure Active Directory escolhendo o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure Active Directory.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Por exemplo, *MSAapp1*.
1. Em **Tipos de conta com suporte**, escolha **Contas em qualquer diretório organizacional (Qualquer diretório do Azure Active Directory – Multilocatário) e contas Microsoft pessoais (por exemplo, Skype, Xbox)** .
1. Em **URI de Redirecionamento (opcional)** , escolha **Web** e insira `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` na caixa de texto. Substitua `<tenant-name>` pelo nome de seu locatário do Azure AD B2C.
1. Escolha **Registrar**
1. Registre a **ID do Aplicativo (do cliente)** mostrada na página Visão Geral do aplicativo. Ela será necessária durante a configuração do provedor de declarações em uma seção posterior.
1. Escolha **Certificados e Segredos**
1. Clique em **Novo segredo do cliente**
1. Insira uma **Descrição** para o segredo, por exemplo *Segredo do cliente do aplicativo MSA*e clique em **Adicionar**.
1. Registre a senha do aplicativo mostrada na coluna **Valor**. Você usará esse valor na próxima seção.

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
            <Item Key="UsePolicyInRedirectUri">0</Item>
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

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com Azure Active Directory B2C ocorre por meio de um aplicativo que você registra em seu locatário B2C. Esta seção lista etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-a para *SignUpSignInMSA.xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInMSA`.
1. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_msa`
1. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder à ID do percurso do usuário que você criou (SignUpSignInMSA).
1. Salve suas alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
1. Verifique se o aplicativo Azure Active Directory B2C que você criou na seção anterior (ou concluindo os pré-requisitos, por exemplo *webapp1* ou *testapp1*) está selecionado no campo **Selecionar aplicativo** e, em seguida, teste-o clicando em **Executar agora**.
1. Escolha o botão **Conta da Microsoft** e entre.

    Se a operação de entrada for bem-sucedida, você será redirecionado para `jwt.ms`, que exibe o token decodificado, semelhante a:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://<tenant-name>.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
