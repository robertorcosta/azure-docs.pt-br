---
title: Configurar a inscrição e entrada com a conta do QQ usando o Azure Active Directory B2C
description: Forneça a inscrição e entrada aos consumidores com contas do QQ em seus aplicativos usando o Azure Active Directory B2C.
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
ms.openlocfilehash: d4d4ae72594532056c0e09a78898abb68ebc1dcf
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584758"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do QQ usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="create-a-qq-application"></a>Criar um aplicativo QQ

Para usar uma conta do QQ como um provedor de identidade no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do QQ, poderá se inscrever em [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

### <a name="register-for-the-qq-developer-program"></a>Registrar-se no programa de desenvolvedores do QQ

1. Entre no [portal do desenvolvedor do QQ](http://open.qq.com) com suas credenciais de conta do QQ.
1. Depois de entrar, acesse [https://open.qq.com/reg](https://open.qq.com/reg) para registrar-se como desenvolvedor.
1. Selecione **个人** (desenvolvedor individual).
1. Insira as informações necessárias e selecione **下一步** (próxima etapa).
1. Conclua o processo de verificação de email. Você precisará aguardar alguns dias para ser aprovado depois de registrar-se como desenvolvedor.

### <a name="register-a-qq-application"></a>Registrar um aplicativo do QQ

1. Acesse [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Selecione **应用管理**(gerenciamento de aplicativos).
1. Selecione **创建应用** (criar aplicativo) e insira as informações necessárias.
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` em **授权回调域** (URL de retorno de chamada). Por exemplo, se `tenant_name` for contoso, defina a URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selecione **创建应用** (criar aplicativo).
1. Na página de confirmação, selecione **应用管理**(gerenciamento de aplicativos) para retornar à página de gerenciamento de aplicativos.
1. Selecione **查看** (exibir) ao lado do aplicativo que você criou.
1. Selecione **修改** (editar).
1. Copie a **ID DO APLICATIVO** e a **CHAVE DO APLICATIVO**. Você precisa de ambos os valores para adicionar o provedor de identidade para seu locatário.

::: zone pivot="b2c-user-flow"

## <a name="configure-qq-as-an-identity-provider"></a>Configurar o QQ como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade** e, em seguida, selecione **QQ (versão prévia)**.
1. Insira um **Nome**. Por exemplo, *QQ*.
1. Para a **ID do cliente**, insira a ID do aplicativo QQ que você criou anteriormente.
1. Para o **segredo do cliente**, insira a chave do aplicativo que você registrou.
1. Selecione **Salvar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que registrou anteriormente no seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **Nome** para a chave de política. Por exemplo, `QQSecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Em **Segredo**, insira o segredo do cliente que você registrou anteriormente.
9. Para **Uso de chave**, selecione `Signature`.
10. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se desejar que os usuários entrem usando uma conta do QQ, você precisará definir a conta como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta QQ como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAUTH">
          <DisplayName>QQ</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">qq</Item>
            <Item Key="authorization_endpoint">https://graph.qq.com/oauth2.0/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://graph.qq.com/oauth2.0/token</Item>
            <Item Key="ClaimsEndpoint">https://graph.qq.com/oauth2.0/me</Item>
            <Item Key="scope">get_user_info</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="ClaimsResponseFormat">JsonP</Item>
            <Item Key="ResponseErrorCodeParamName">error</Item>
            <Item Key="external_user_identity_claim_id">openid</Item>
            <Item Key="client_id">Your QQ application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_QQSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="openid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="qq.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. Defina **client_id** para a ID do aplicativo de registro de aplicativo.
5. Salve o arquivo.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com sua conta do QQ. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das telas de inscrição/entrada. Para disponibilizá-lo, crie uma duplicata de um percurso de usuário de modelo existente e, em seguida, modifique-o para que ele também tenha o provedor de identidade QQ.

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInQQ`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição/entrada. Se você adicionar um elemento **ClaimsProviderSelection** para uma conta QQ, um novo botão será exibido quando um usuário chegar à página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada do usuário que você criou.
2. Em **ClaimsProviderSelects**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `QQExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação é para Azure AD B2C se comunicar com uma conta do QQ para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** usando o mesmo valor de ID usado para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a ID do perfil técnico você já criou. Por exemplo, `QQ-OAuth`.

3. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-qq-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade QQ a um fluxo de usuário 

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário que você deseja para o provedor de identidade QQ.
1. Em **provedores de identidade social**, selecione **QQ**.
1. Selecione **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar fluxo de usuário**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-o como *SignUpSignInQQ.xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInQQ`.
1. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_QQ`
1. Atualize o valor do atributo **referenceid** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignQQ).
1. Salve as alterações, carregue o arquivo.
1. Na página **Políticas personalizadas**, selecione **B2C_1A_signup_signin**.
1. Para **Selecionar aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione **executar agora** e selecione QQ para entrar com QQ e testar a política personalizada.

::: zone-end
