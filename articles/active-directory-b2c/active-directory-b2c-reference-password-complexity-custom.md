---
title: Configurar a complexidade de senha usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como configurar os requisitos de complexidade de senha usando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e8718a04f9d63897b2d2472dd0cdffb196c41435
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949782"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a complexidade da senha usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Active Directory B2C (Azure AD B2C), você pode configurar os requisitos de complexidade para senhas que são fornecidas por um usuário ao criar uma conta. Por padrão, o Azure AD B2C usa senhas **Fortes**. Este artigo mostra como configurar a complexidade de senha em [políticas personalizadas](active-directory-b2c-overview-custom.md). Também é possível configurar a complexidade da senha em [fluxos de usuário](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas no Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Adicionar os elementos

1. Copie o arquivo *SignUpOrSignIn.xml* que você baixou com o pacote de início e nomeie-o *SignUpOrSignInPasswordComplexity.xml*.
2. Abra o arquivo *SignUpOrSignInPasswordComplexity.xml* e mude **PolicyId** e **PublicPolicyUri** para um novo nome de política. Por exemplo, *B2C_1A_signup_signin_password_complexity*.
3. Adicione os seguintes elementos **ClaimType** com os identificadores `newPassword` e `reenterPassword`:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [Predicates](predicates.md) tem os tipos de método `IsLengthRange` ou `MatchesRegex`. O tipo `MatchesRegex` é usado para corresponder a uma expressão regular. O tipo `IsLengthRange` usa um comprimento de cadeia de caracteres mínimo e máximo. Adicione um elemento **Predicates** ao elemento **BuildingBlocks**, se ele não existir com os seguintes elementos **Predicate**:

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Cada elemento **InputValidation** é construído usando os elementos **Predicate** definidos. Esse elemento permite a execução de agregações boolianas semelhantes a `and` e `or`. Adicione um elemento **InputValidations** ao elemento **BuildingBlocks**, se ele não existir com o seguinte elemento **InputValidation**:

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. Certifique-se de que o perfil técnico **PolicyProfile** contém os seguintes elementos:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Salve o arquivo da política.

## <a name="test-your-policy"></a>Testar sua política

Ao testar seus aplicativos no Azure AD B2C, pode ser útil ter o token do Azure AD B2C retornado ao `https://jwt.ms` para poder examinar as declarações.

### <a name="upload-the-files"></a>Carregar os arquivos

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Na página de políticas personalizadas, clique em **Carregar Política**.
6. Habilite **Substituir a política se ela existir** e, em seguida, navegue até o arquivo *SignUpOrSignInPasswordComplexity.xml* e selecione-o.
7. Clique em **Carregar**.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que você alterou. Por exemplo, *B2C_1A_signup_signin_password_complexity*.
2. Para **Aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token, a **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**.
4. Selecione **Inscrever-se agora**, insira um endereço de email e insira uma nova senha. Algumas restrições de senhas são apresentadas. Termine de inserir as informações do usuário e clique em **Criar**. Você deverá ver o conteúdo do token retornado.

## <a name="next-steps"></a>Próximos passos

- Saiba como [Configurar a alteração da senha usando políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


