---
title: Configure o login com uma conta microsoft usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como usar políticas personalizadas para habilitar a Microsoft Account (MSA) como provedor de identidade usando o protocolo OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188110"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada com uma conta Microsoft usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como ativar o login para usuários de uma conta Microsoft usando [políticas personalizadas](custom-policy-overview.md) no Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md).
- Se você ainda não tem uma conta [https://www.live.com/](https://www.live.com/)microsoft, crie uma em .

## <a name="register-an-application"></a>Registrar um aplicativo

Para habilitar o login para usuários com uma conta Microsoft, você precisa registrar um aplicativo dentro do inquilino Azure AD. Locatário do Azure AD não é o mesmo que seu locatário do Azure AD B2C.

1. Faça login no [portal Azure](https://portal.azure.com).
1. Certifique-se de que está usando o diretório que contém o inquilino Azure AD selecionando o filtro **de assinatura Diretório +** no menu superior e escolhendo o diretório que contém o inquilino Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Digite um **nome** para sua inscrição. Por exemplo, *MSAapp1*.
1. Em **tipos de conta suportados,** selecione Contas em qualquer diretório organizacional e contas pessoais da Microsoft **(por exemplo, Skype, Xbox, Outlook.com)**.
1. Em **Redirecionar URI (opcional)**, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` selecione **Web** e digite na caixa de texto. Substitua pelo `your-tenant-name` nome de inquilino Azure AD B2C.
1. Selecionar **Registro**
1. Registre o **ID do aplicativo (cliente)** mostrado na página Visão Geral do aplicativo. Você precisa disso quando configurar o provedor de sinistros em uma seção posterior.
1. Selecione **Certificados & segredos**
1. Clique em **Novo cliente secreto**
1. Digite uma **descrição** do segredo, por exemplo, *MSA Application Client Secret*e, em seguida, clique em **Adicionar**.
1. Registre a senha do aplicativo mostrada na coluna **Valor.** Você usa este valor na próxima seção.

## <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

Se você quiser `family_name` obter `given_name` as e reivindicações do Azure AD, você pode configurar reivindicações opcionais para o seu aplicativo no portal Azure UI ou manifesto de aplicativo. Para obter mais informações, consulte [Como fornecer reclamações opcionais para o seu aplicativo Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Faça login no [portal Azure](https://portal.azure.com). Pesquise **Azure Active Directory** e selecione-o.
1. Na seção **Gerenciar,** selecione **registros do Aplicativo**.
1. Selecione o aplicativo para o aplicativo que deseja configurar reivindicações opcionais na lista.
1. Na seção **Gerenciar,** selecione **Configuração de Token (visualização)**.
1. Selecione **Adicionar reivindicação opcional**.
1. Selecione o tipo de token que deseja configurar.
1. Selecione as reivindicações opcionais a serem adicionais.
1. Clique em **Adicionar**.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Agora que você criou o aplicativo no seu inquilino Azure AD, você precisa armazenar o segredo do cliente desse aplicativo no seu inquilino Azure AD B2C.

1. Faça login no [portal Azure](https://portal.azure.com/).
1. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `MSASecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Em **Segredo,** digite o segredo do cliente que você gravou na seção anterior.
1. Para **Uso de chave**, selecione `Signature`.
1. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Para permitir que seus usuários entrem usando uma conta microsoft, você precisa definir a conta como um provedor de sinistros com o qual o Azure AD B2C pode se comunicar através de um ponto final. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure AD como um provedor de declarações, adicionando o elemento **ClaimsProvider** no arquivo de extensão de sua política.

1. Abra o arquivo de diretiva *TrustFrameworkExtensions.xml.*
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

1. Substitua o valor de **client_id** pelo ID de *aplicativo (cliente)* do aplicativo Azure AD que você gravou anteriormente.
1. Salve o arquivo.

Agora você configurou sua política para que o Azure AD B2C saiba como se comunicar com seu aplicativo de conta Microsoft no Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Antes de continuar, carregue a política modificada para confirmar que ela não tem nenhum problema até agora.

1. Navegue até o seu inquilino Azure AD B2C no portal Azure e selecione **Identity Experience Framework**.
1. Na **página Políticas personalizadas,** selecione **Enviar diretiva personalizada**.
1. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
1. Clique em **Carregar**.

Se nenhum erro for exibido no portal, continue até a próxima seção.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste momento, você configurou o provedor de identidade, mas ainda não está disponível em nenhuma das telas de inscrição ou login. Para torná-lo disponível, crie uma duplicata de uma jornada de usuário de modelo existente e modifique-a para que ela também tenha o provedor de identidade da conta Microsoft.

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
1. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
1. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
1. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição ou de entrada. Se você adicionar um elemento **ClaimsProviderSelection** para uma conta Microsoft, um novo botão será exibido quando um usuário aterrissa na página.

1. No arquivo *TrustFrameworkExtensions.xml*, localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou.
1. Em **ClaimsProviderSelects**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. A ação, neste caso, é que o Azure AD B2C se comunique com uma conta da Microsoft para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
1. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que você use o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Atualize o valor do **TechnicalProfileReferenceId** para corresponder ao `Id` do valor no elemento **TechnicalProfile** do provedor de sinistros que você adicionou anteriormente. Por exemplo, `MSA-OIDC`.

1. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de um aplicativo que você registra no seu inquilino B2C. Esta seção lista etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-a para *SignUpSignInMSA.xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInMSA`.
1. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_msa`
1. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder ao ID da jornada do usuário que você criou anteriormente (SignUpSignInMSA).
1. Salve suas alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
1. Certifique-se de que o aplicativo Azure AD B2C que você criou na seção anterior (ou completando os pré-requisitos, por exemplo *webapp1* ou *testapp1*) seja selecionado no campo **Selecionar aplicativo** e, em seguida, testá-lo clicando em **Executar agora**.
1. Selecione o botão **Conta Microsoft** e faça login.

    Se a operação de login for bem sucedida, você será redirecionado para o `jwt.ms` qual exibe o Token Decodificado, semelhante a:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
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
