---
title: Mantenha-me conectado no Azure Active Directory B2C
description: Aprenda a configurar o KMSI (Mantenha-me conectado) no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a27487fa69888b02883c3d9a2151887f41afc45
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189371"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Habilitar o KMSI (Mantenha-me conectado) no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Você pode habilitar a funcionalidade KMSI (Mantenha-me conectado) para usuários de seus aplicativos Web e nativos que têm contas locais em seu diretório Azure Active Directory B2C (Azure AD B2C). Esse recurso concede acesso a usuários que retornam ao seu aplicativo sem solicitar que eles reinsiram seu nome de usuário e senha. Esse acesso é revogado quando o usuário sai do serviço.

Os usuários não devem habilitar essa opção em computadores públicos.

![Exemplo de página de entrada de inscrição mostrando uma caixa de seleção Mantenha-me conectado](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

- Um locatário Azure AD B2C configurado para permitir a entrada da conta local. KMSI não tem suporte para contas de provedor de identidade externa.
- Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Configurar o identificador de página

Para habilitar o KMSI, defina o elemento de definição de conteúdo `DataUri` como [identificador de página](contentdefinitions.md#datauri) `unifiedssp` e versão *1.1.0* ou superior [da página](page-layout.md) .

1. Abra o arquivo de extensão da sua política. Por exemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>  . Esse arquivo de extensão é um dos arquivos de política incluídos no pacote de início de política personalizada, que você deve ter obtido no pré-requisito, [introdução às políticas personalizadas](custom-policy-get-started.md).
1. Pesquise o elemento **BuildingBlocks**. Se o elemento não existir, adicione-o.
1. Adicione o elemento **ContentDefinitions** ao elemento **BuildingBlocks** da política.

    Sua política personalizada deve ser semelhante ao seguinte trecho de código:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Salve o arquivo de extensões.



## <a name="configure-a-relying-party-file"></a>Configurar um arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Abra o arquivo de política personalizada. Por exemplo, *SignUpOrSignin.xml*.
1. Se ele ainda não existir, adicione um `<UserJourneyBehaviors>` nó filho ao nó `<RelyingParty>`. Ele deve estar localizado imediatamente após `<DefaultUserJourney ReferenceId="User journey Id" />`, por exemplo: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Adicione o seguinte nó como um filho do elemento `<UserJourneyBehaviors>`.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** -indica como a sessão é estendida pelo tempo especificado em `SessionExpiryInSeconds` e `KeepAliveInDays`. O valor de `Rolling` (padrão) indica que a sessão é estendida toda vez que o usuário executa a autenticação. O valor `Absolute` indica que o usuário é forçado a autenticar novamente após o período de tempo especificado.

    - **SessionExpiryInSeconds** -o tempo de vida de cookies de sessão quando se *mantém conectado* não está habilitado, ou se um usuário não seleciona *manter-me conectado*. A sessão expira depois que `SessionExpiryInSeconds` foi aprovada ou o navegador é fechado.

    - **KeepAliveInDays** -o tempo de vida de cookies de sessão quando se *mantém conectado* está habilitado e o usuário seleciona *Mantenha-me conectado*.  O valor de `KeepAliveInDays` tem precedência sobre o valor de `SessionExpiryInSeconds` e determina o tempo de expiração da sessão. Se um usuário fechar o navegador e abri-lo novamente mais tarde, ele ainda poderá entrar silenciosamente, desde que esteja dentro do período de tempo KeepAliveInDays.

    Para obter mais informações, consulte [comportamentos de jornada do usuário](relyingparty.md#userjourneybehaviors).

Recomendamos que você defina o valor de SessionExpiryInSeconds como um período curto (1200 segundos), enquanto o valor de KeepAliveInDays pode ser definido como um período relativamente longo (30 dias), conforme mostrado no exemplo a seguir:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

4. Salve as alterações e, em seguida, faça o upload do arquivo.
5. Para testar a política personalizada que você carregou, no portal do Azure, vá até a página de política e, em seguida, clique em **Executar agora**.

Você pode encontrar a política de exemplo [aqui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
