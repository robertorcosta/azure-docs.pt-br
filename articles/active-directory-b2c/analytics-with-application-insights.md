---
title: Acompanhar o comportamento do usuário com Application Insights
titleSuffix: Azure AD B2C
description: Saiba como habilitar logs de eventos em Application Insights de Azure AD B2C percursos do usuário usando políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67ea7324419d86fa5b5c23a2f0aa5f8c057495d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385970"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Rastrear o comportamento do usuário no Azure Active Directory B2C usando o Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

O Azure Active Directory B2C (Azure AD B2C) dá suporte ao envio de dados de eventos diretamente para [Application insights](../azure-monitor/app/app-insights-overview.md) usando a chave de instrumentação fornecida para Azure ad B2C.  Com um perfil técnico Application Insights, você pode obter logs de eventos detalhados e personalizados para que seus percursos do usuário:

* Obter insights sobre o comportamento do usuário.
* Solucionar problemas de suas próprias políticas em desenvolvimento ou em produção.
* Medir o desempenho.
* Criar notificações do Application Insights.

## <a name="how-it-works"></a>Como funciona

O perfil técnico [Application insights](application-insights-technical-profile.md) define um evento de Azure ad B2C. O perfil especifica o nome do evento, as reivindicações registradas e a chave de instrumentação. Para postar um evento, o perfil técnico é adicionado como uma etapa de orquestração em uma [jornada do usuário](userjourneys.md).

O Application Insights pode unificar os eventos usando uma ID de correlação para registrar uma sessão do usuário. O Application Insights disponibiliza o evento e a sessão em segundos e apresenta muitas ferramentas analíticas, de exportação e visualização.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando você estiver usando o Application Insights com o Azure AD B2C, tudo o que você precisa fazer é criar um recurso e obter a chave de instrumentação. Para obter informações, consulte [criar um recurso de Application insights](../azure-monitor/app/create-new-resource.md)

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém sua assinatura do Azure selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém sua assinatura. Esse locatário não é o seu locatário do Azure Active Directory B2C.
3. Escolher **criar um recurso** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **Application Insights**.
4. Clique em **Criar**.
5. Insira um **nome** para o recurso.
6. Para **tipo de aplicativo**, selecione **aplicativo web ASP.NET**.
7. Para **grupo de recursos**, selecione um grupo existente ou digite um nome para um novo grupo.
8. Clique em **Criar**.
4. Depois de criar o recurso Application Insights, abra-o, expanda **Essentials** e copie a chave de instrumentação.

![Visão geral do Application Insights e Chave de Instrumentação](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definir declarações

Uma declaração fornece um armazenamento temporário de dados durante uma execução de política de Azure AD B2C. O [esquema de declarações](claimsschema.md) é o lugar em que você declara suas declarações.

1. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema](claimsschema.md). Se o elemento não existir, adicione-o.
1. Adicione as seguintes declarações ao elemento **ClaimsSchema**. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Adicionar novos perfis técnicos

Perfis técnicos podem ser considerados funções na Estrutura de Experiência de Identidade do Azure Active Directory B2C. Esta tabela define os perfis técnicos que são usados para abrir uma sessão e postar eventos.

| Perfil técnico | Tarefa |
| ----------------- | -----|
| AppInsights – comum | O conjunto comum de parâmetros a ser incluído em todos os perfis técnicos do Azure insights. |
| AppInsights-SignInRequest | Registra um `SignInRequest` evento com um conjunto de declarações quando uma solicitação de entrada é recebida. |
| AppInsights-usersignup | Registra um `UserSignUp` evento quando o usuário aciona a opção de inscrição em uma jornada de inscrição/entrada. |
| AppInsights-SignInComplete | Registra um `SignInComplete` evento após a conclusão bem-sucedida de uma autenticação, quando um token foi enviado para o aplicativo de terceira parte confiável. |

Adicione os perfis para o *trustframeworkextensions. XML* arquivo do starter pack. Adicione esses elementos para o **ClaimsProviders** elemento:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Altere a chave de instrumentação no perfil técnico `AppInsights-Common` para o GUID que seu recurso do Application Insights fornece.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Adicionar os perfis técnicos como etapas de orquestração

Chame `AppInsights-SignInRequest` como etapa de orquestração 2 para rastrear se uma solicitação de inscrição/entrada foi recebida:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente *antes* da etapa de orquestração `SendClaims`, adicione uma nova etapa que chama `AppInsights-UserSignup`. É disparado quando o usuário seleciona o botão de inscrição em um percurso de inscrição/entrada.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente após a etapa de orquestração `SendClaims` chame `AppInsights-SignInComplete`. Esta etapa mostra um percurso concluído com êxito.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Após adicionar as novas etapas de orquestração, renumere as etapas sequencialmente sem ignorar números inteiros de 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Carregar seu arquivo, executar a política e exibir eventos

Faça upload do arquivo *TrustFrameworkExtensions.xml* no seu locatário. Em seguida, chame a política da terceira parte confiável de seu aplicativo ou use **Executar agora** no portal do Azure. Em segundos, os eventos estarão disponíveis no Application Insights.

1. Abra o recurso do **Application Insights** no locatário do Azure Active Directory.
2. Selecione **Usage**  >  **eventos**de uso.
3. Defina **Durante** para **Última hora** e **Por** para **3 minutos**.  Talvez você precise selecionar **Atualizar** para exibir os resultados.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>Adicional Coletar mais dados

Adicione tipos de declarações e eventos ao percurso do usuário para ajustar às suas necessidades. Você pode usar [resolvedores de declaração](claim-resolver-overview.md) ou qualquer tipo de declaração de cadeia de caracteres, adicionar as declarações adicionando um elemento de **declaração de entrada** ao evento Application insights ou ao perfil técnico AppInsights-comum.

- **ClaimTypeReferenceId** é a referência a um tipo de declaração.
- **PartnerClaimType** é o nome da propriedade que é exibido no Azure Insights. Use a sintaxe da `{property:NAME}`, onde `NAME` é a propriedade que está sendo adicionada ao evento.
- **DefaultValue** usa qualquer valor de cadeia ou o resolvedor de reclamações.

```xml
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Application insights](application-insights-technical-profile.md) perfil técnico na referência do IEF. 
