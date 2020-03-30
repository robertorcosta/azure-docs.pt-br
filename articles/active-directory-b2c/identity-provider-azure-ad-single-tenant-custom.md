---
title: Configure o login com uma conta AD do Azure usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Configure o logon com uma conta do Azure Active Directory no Azure Active Directory B2C usando políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5a6c85ebed7271655745de45694542fb359836e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188403"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar login com uma conta do Azure Active Directory usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como ativar o login para usuários de uma organização do Azure Active Directory (Azure AD) usando [políticas personalizadas](custom-policy-overview.md) no Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Registrar um aplicativo

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

1. Faça login no [portal Azure](https://portal.azure.com).
1. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD organizacional (por exemplo, contoso.com). Selecione o **filtro de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Digite um **nome** para sua inscrição. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção padrão de **Contas neste diretório organizacional apenas** para este aplicativo.
1. Para o **URI redirecionar**, aceite o valor da **Web**e digite a URL a seguir em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu inquilino Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Selecione **Registrar**. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Selecione **Certificados & segredos**e selecione **Novo segredo do cliente**.
1. Digite uma **descrição** do segredo, selecione uma expiração e, em seguida, **selecione Adicionar**. Registre o **valor** do segredo para uso em uma etapa posterior.

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

Você precisa armazenar a chave do aplicativo que criou em seu locatário do Azure AD B2C.

1. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C. Selecione o **filtro de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Em **Políticas,** selecione **Identity Experience Framework**.
1. Selecione **as teclas de diretiva** e **selecione Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `ContosoAppSecret`.  O `B2C_1A_` prefixo é adicionado automaticamente ao nome da sua chave quando ela é criada, de modo que sua referência na seção XML na seção seguinte é *B2C_1A_ContosoAppSecret*.
1. Em **Segredo,** digite o segredo do seu cliente que você gravou anteriormente.
1. Para **Uso de chave**, selecione `Signature`.
1. Selecione **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se você quiser que os usuários entrem usando o Azure AD, precisará definir o Azure AD como um provedor de declarações com o qual o Azure AD B2C pode se comunicar por meio de um endpoint. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure AD como um provedor de declarações adicionando o Azure AD ao elemento **ClaimsProvider** no arquivo de extensão de sua política.

1. Abra o arquivo *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. No elemento **ClaimsProvider**, atualize o valor do **Domínio** para um valor exclusivo que possa ser usado para diferenciá-lo de outros provedores de identidade. Por exemplo, `Contoso`. Você não coloque um `.com` no final dessa configuração de domínio.
5. No elemento **ClaimsProvider**, atualize o valor de **DisplayName** para um nome amigável para o provedor de declarações. Esse valor não é usado atualmente.

### <a name="update-the-technical-profile"></a>Atualizar o perfil técnico

Para obter um token do ponto de extremidade do Azure AD, você precisa definir os protocolos que o Azure AD B2C deve usar para se comunicar com o Azure AD. Isso é feito dentro do elemento **TechnicalProfile** de **ClaimsProvider**.

1. Atualize a ID do **TechnicalProfile** elemento. Este ID é usado para se referir a esse perfil `OIDC-Contoso`técnico de outras partes da política, por exemplo.
1. Atualize o valor de **DisplayName**. Esse valor será exibido no botão de entrada em sua tela de entrada.
1. Atualize o valor para **Descrição**.
1. O Azure AD usa o protocolo OpenID Connect, portanto, verifique se o valor para **Protocolo** é `OpenIdConnect`.
1. Defina o valor de **METADATA** para `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`, em que `tenant-name` é o nome do seu locatário do Azure AD. Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Defina **client_id** para a ID do aplicativo de registro de aplicativo.
1. Em **CryptographicKeys,** atualize o valor do **StorageReferenceId** para o nome da chave de diretiva que você criou anteriormente. Por exemplo, `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

A essa altura, você já terá configurado a política, de forma que o Azure AD B2C saiba como se comunicar com o diretório do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
1. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
1. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste momento, o provedor de identidade foi configurado, mas ainda não está disponível em nenhuma das páginas de inscrição/login. Para torná-lo disponível, crie uma duplicata de uma jornada de usuário de modelo existente e, em seguida, modifique-a para que ele também tenha o provedor de identidade Azure AD:

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
1. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
1. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
1. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
1. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma página de inscrição/login. Se você adicionar um elemento **ClaimsProviderSelection** para o Azure AD, um novo botão será exibido quando um usuário chegar na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada do usuário que você criou em *TrustFrameworkExtensions.xml*.
1. Em **ClaimsProviderSelections**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com o Azure AD para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações do Azure AD:

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
1. Adicione o seguinte elemento **ClaimsExchange** usando o mesmo valor de **ID** usado para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a **ID** do perfil técnico você já criou. Por exemplo, `OIDC-Contoso`.

1. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre através de um aplicativo que você registra no seu inquilino B2C. Esta seção lista etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-o para *SignUpSignInContoso.xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInContoso`.
1. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Atualize o valor do atributo **ReferenceId** no **DefaultUserJourney** para corresponder ao ID da jornada do usuário que você criou anteriormente. Por exemplo, *Inscreva-seSignInContoso*.
1. Salve as alterações e carregue o arquivo.
1. Em **políticas personalizadas,** selecione a nova diretiva na lista.
1. Na versão do **aplicativo Selecionar,** selecione o aplicativo Azure AD B2C que você criou anteriormente. Por exemplo, *testapp1*.
1. Copie o **ponto final da Execução agora** e abra-o em uma janela privada do navegador, por exemplo, o Modo Incógnito no Google Chrome ou uma janela InPrivate no Microsoft Edge. A abertura em uma janela privada do navegador permite que você teste toda a jornada do usuário, não usando nenhuma credencial AD atualmente armazenada em cache.
1. Selecione o botão de login azure AD, por exemplo, *Contoso Employee*, e, em seguida, digite as credenciais para um usuário em seu inquilino organizacional Azure AD. Você é solicitado a autorizar a solicitação e, em seguida, inserir informações para o seu perfil.

Se o processo de login for bem `https://jwt.ms`sucedido, seu navegador será redirecionado, o que exibe o conteúdo do token retornado pelo Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

Ao trabalhar com políticas personalizadas, às vezes você pode precisar de informações adicionais ao solucionar problemas de uma política durante seu desenvolvimento.

Para ajudar a diagnosticar problemas, você pode colocar temporariamente a política no "modo desenvolvedor" e coletar logs com o Azure Application Insights. Descubra como no [Azure Active Directory B2C: Collecting Logs](troubleshoot-with-application-insights.md).
