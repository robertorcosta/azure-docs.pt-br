---
title: Passar um token de acesso por meio de uma política personalizada para seu aplicativo
titleSuffix: Azure AD B2C
description: Saiba como você pode passar um token de acesso para provedores de identidade OAuth 2,0 como uma declaração por meio de uma política personalizada para seu aplicativo no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f7122035f8d70cb91f4ec4f64e1dd4f7b2842b8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949833"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Passar um token de acesso por meio de uma politica personalizada para seu aplicativo no Azure Active Directory B2C

Uma [política personalizada](active-directory-b2c-get-started-custom.md) no Azure Active Directory B2C (Azure ad B2C) fornece aos usuários de seu aplicativo uma oportunidade de se inscrever ou entrar com um provedor de identidade. Quando isso acontece, o Azure AD B2C recebe um [token de acesso](active-directory-b2c-reference-tokens.md) do provedor de identidade. O Azure Active Directory B2C usa esse token para recuperar informações sobre o usuário. Adicione um tipo de declaração e declaração de saída para a sua política personalizada para passar o token para os aplicativos que você registra no ?Azure Active Directory B2C.

O Azure AD B2C é compatível com passagem do token de acesso dos provedores de identidade [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) e [OpenID Connect](active-directory-b2c-reference-oidc.md). Para todos os outros provedores de identidade, a declaração é retornada em branco.

## <a name="prerequisites"></a>Pré-requisitos

* Sua política personalizada é configurada com um provedor de identidade OAuth 2.0 ou OpenID Connect.

## <a name="add-the-claim-elements"></a>Adicione os elementos de declaração

1. Abre seu arquivo *TrustframeworkExtensions.xml* e adicione o seguinte elemento **ClaimType** com um identificador de `identityProviderAccessToken` para o elemento **ClaimsSchema**:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Adicione o elemento **OutputClaim** ao elemento **TechnicalProfile** para cada provedor de identidade do OAuth 2.0 que você gostaria de ter o token de acesso. O exemplo a seguir mostra o elemento adicionado ao perfil técnico do Facebook:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Salve o arquivo *TrustFrameworkExtensions.xml*.
4. Abra seu arquivo de política de terceira parte confiável, como *Signuporsignin*e adicione o elemento **OutputClaim** para **TechnicalProfile**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Salve o arquivo da política.

## <a name="test-your-policy"></a>Testar sua política

Ao testar seus aplicativos no Azure AD B2C, pode ser útil ter o token do Azure AD B2C retornado ao `https://jwt.ms` para poder examinar as declarações.

### <a name="upload-the-files"></a>Carregar os arquivos

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C clicando no filtro **diretório + assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Na página de políticas personalizadas, clique em **Carregar Política**.
6. Habilite **Substituir a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
7. Escolha **Carregar**.
8. Repita as etapas 5 a 7 para o arquivo de terceira parte confiável, como *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que você alterou. Por exemplo, *B2C_1A_signup_signin*.
2. Para **Aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token no exemplo a seguir, o **URL de resposta** deve mostrar `https://jwt.ms`.
3. Selecione **Executar Agora**.

    Você deverá ver algo semelhante ao texto a seguir:

    ![Token decodificado em jwt.ms com idp_access_token bloco realçado](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre tokens na [referência de token de Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).
