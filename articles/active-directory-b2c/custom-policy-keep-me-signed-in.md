---
title: Mantenha-me conectado no Diretório Ativo Do Azure B2C
description: Aprenda a configurar o KMSI (Mantenha-me conectado) no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0de94cdce1d7f0e9da9d2844b300956ad6f6970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330834"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Habilitar o KMSI (Mantenha-me conectado) no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Você pode habilitar a funcionalidade Keep Me Signed In (KMSI) para usuários de seus aplicativos web e nativos que tenham contas locais no diretório ativo do Azure B2C (Azure AD B2C). Esse recurso concede acesso aos usuários que retornam ao seu aplicativo sem pedir que eles insiram novamente seu nome de usuário e senha. Esse acesso é revogado quando o usuário sai do serviço.

Os usuários não devem habilitar essa opção em computadores públicos.

![Exemplo de login mostrando um Keep me assinado na caixa de seleção](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Pré-requisitos

- Um inquilino Azure AD B2C que está configurado para permitir o login da conta local. O KMSI não tem suporte para contas de provedores de identidade externos.
- Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Configure o identificador de página

Para habilitar o KMSI, defina o `DataUri` elemento de definição de conteúdo para [identificador de](contentdefinitions.md#datauri) `unifiedssp` página e página [versão](page-layout.md) *1.1.0* ou superior.

1. Abra o arquivo de extensão da sua política. Por exemplo, <em> `SocialAndLocalAccounts/` </em>. Este arquivo de extensão é um dos arquivos de diretiva incluídos no pacote de inicialização de políticas personalizadas, que você deve ter obtido no pré-requisito, [Comece com políticas personalizadas](custom-policy-get-started.md).
1. Pesquise o elemento **BuildingBlocks**. Se o elemento não existir, adicione-o.
1. Adicione o elemento **ContentDefinitions** ao elemento **BuildingBlocks** da diretiva.

    Sua política personalizada deve parecer com o seguinte trecho de código:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Adicione os metadados ao perfil técnico auto-afirmado

Para adicionar a caixa de seleção KMSI à página `setting.enableRememberMe` de inscrição e login, defina os metadados como falsos. Substituir os perfis técnicos SelfAsserted-LocalAccountSignin-Email no arquivo de extensão.

1. Localize o elemento ClaimsProviders. Se o elemento não existir, adicione-o.
1. Adicione o seguinte provedor de sinistros ao elemento Provedores de Sinistros:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Salve o arquivo de extensões.

## <a name="configure-a-relying-party-file"></a>Configure um arquivo de parte que depende

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Abra o arquivo de política personalizada. Por exemplo, *SignUpOrSignin.xml*.
1. Se ele ainda não existir, `<UserJourneyBehaviors>` adicione um `<RelyingParty>` nó infantil ao nó. Deve ser localizado imediatamente `<DefaultUserJourney ReferenceId="User journey Id" />`após, por `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`exemplo: .
1. Adicione o seguinte nó como um filho do elemento `<UserJourneyBehaviors>`.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Indica como a sessão é `SessionExpiryInSeconds` estendida `KeepAliveInDays`pelo tempo especificado e . O `Rolling` valor (padrão) indica que a sessão é estendida toda vez que o usuário realiza a autenticação. O `Absolute` valor indica que o usuário é forçado a reautenticar após o período de tempo especificado.

    - **SessionExpiryInSeconds** - A vida útil dos cookies de sessão quando *me mantém conectado* não está ativada ou se um usuário não selecionar *me manter conectado*. A sessão expira `SessionExpiryInSeconds` depois de aprovada, ou o navegador é fechado.

    - **KeepAliveInDays** - A vida útil dos cookies de sessão quando *me mantém conectado* está ativada e o usuário seleciona *me manter conectado*.  O valor `KeepAliveInDays` de tem `SessionExpiryInSeconds` precedência sobre o valor, e dita o tempo de validade da sessão. Se um usuário fechar o navegador e reabri-lo mais tarde, ele ainda pode entrar silenciosamente, desde que esteja dentro do período de tempo do KeepAliveInDays.

    Para obter mais informações, consulte [os comportamentos da jornada do usuário](relyingparty.md#userjourneybehaviors).

Recomendamos que você defina o valor de SessionExpiryInSeconds como sendo um período curto (1200 segundos), enquanto o valor de KeepAliveInDays pode ser definido como um período relativamente longo (30 dias), como mostrado no exemplo a seguir:

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

## <a name="test-your-policy"></a>Testar sua política

1. Salve as alterações e, em seguida, carregue o arquivo.
1. Para testar a política personalizada enviada, no portal Azure, vá até a página de diretiva e selecione **Executar agora**.
1. Digite seu **nome de usuário** e **senha,** **selecione Mantenha-me conectado**e clique em fazer **login**.
1. Retorne ao portal do Azure. Vá para a página de diretiva e, em seguida, selecione **Copiar** para copiar a URL de login.
1. Na barra de endereços `&prompt=login` do navegador, remova o parâmetro de seqüência de consulta, que obriga o usuário a inserir suas credenciais nessa solicitação.
1. No navegador, clique em **Go**. Agora, o Azure AD B2C emitirá um token de acesso sem solicitar que você faça login novamente. 

## <a name="next-steps"></a>Próximas etapas

Encontre a política de amostra [aqui.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)
