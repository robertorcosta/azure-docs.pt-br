---
title: Configurar a complexidade de senha usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Como configurar os requisitos de complexidade de senha usando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4ab196e894fc53b1243ac363f9863d5c7d4e328f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388996"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a complexidade da senha usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Active Directory B2C (Azure AD B2C), você pode configurar os requisitos de complexidade para senhas que são fornecidas por um usuário ao criar uma conta. Por padrão, o Azure AD B2C usa senhas **Fortes**. Este artigo mostra como configurar a complexidade de senha em [políticas personalizadas](custom-policy-overview.md). Também é possível configurar a complexidade da senha em [fluxos de usuário](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.


## <a name="add-the-elements"></a>Adicionar os elementos

Para configurar a complexidade da senha, substitua `newPassword` os `reenterPassword` [tipos de declaração](claimsschema.md) e por uma referência a [validações de predicado](predicates.md#predicatevalidations). O elemento PredicateValidations agrupa um conjunto de predicados para formar uma validação de entrada de usuário que pode ser aplicada a um tipo de declaração. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema](claimsschema.md). Se o elemento não existir, adicione-o.
1. Adicione as `newPassword` `reenterPassword` declarações e ao elemento **ClaimsSchema** .

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. Os [predicados](predicates.md) definem uma validação básica para verificar o valor de um tipo de declaração e retorna true ou false. A validação é feita usando um elemento de método especificado e um conjunto de parâmetros relevantes para o método. Adicione os seguintes predicados ao elemento **BuildingBlocks** imediatamente após o fechamento do `</ClaimsSchema>` elemento:

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Adicione as validações predicadas a seguir ao elemento **BuildingBlocks** imediatamente após o fechamento do `</Predicates>` elemento:

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. Os perfis técnicos a seguir são [Active Directory perfis técnicos](active-directory-technical-profile.md), que lêem e gravam dados em Azure Active Directory. Substitua esses perfis técnicos no arquivo de extensão. Use `PersistedClaims` para desabilitar a política de senha forte. Localize o elemento **ClaimsProviders**.  Adicione os seguintes provedores de declaração da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Salve o arquivo da política.

## <a name="test-your-policy"></a>Testar sua política

### <a name="upload-the-files"></a>Carregar os arquivos

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Na página de políticas personalizadas, clique em **Carregar Política**.
6. Selecione **substituir a política, se ela existir**, e, em seguida, procure e selecione o arquivo de *TrustFrameworkExtensions.xml* .
7. Clique em **Carregar**.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política de inscrição ou entrada. Por exemplo, *B2C_1A_signup_signin*.
2. Para **Aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token, a **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**.
4. Selecione **Inscrever-se agora**, insira um endereço de email e insira uma nova senha. Algumas restrições de senhas são apresentadas. Termine de inserir as informações do usuário e clique em **Criar**. Você deverá ver o conteúdo do token retornado.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Configurar a alteração da senha usando políticas personalizadas no Azure Active Directory B2C](custom-policy-password-change.md).
- Saiba mais sobre os [predicados](predicates.md) e elementos [PredicateValidations](predicates.md#predicatevalidations) na referência de IEF.
