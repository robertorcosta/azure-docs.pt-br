---
title: Defina um perfil técnico do Application Insights em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico do Application Insights em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108569"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico do Application Insights em uma política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) suporta o envio de dados de eventos diretamente para o [Application Insights](../azure-monitor/app/app-insights-overview.md) usando a chave de instrumentação fornecida ao Azure AD B2C.  Com um perfil técnico do Application Insights, você pode obter registros de eventos detalhados e personalizados para suas jornadas de usuário para:

* Obter insights sobre o comportamento do usuário.
* Solucionar problemas de suas próprias políticas em desenvolvimento ou em produção.
* Medir o desempenho.
* Criar notificações do Application Insights.


## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `Proprietary`. O atributo **do manipulador** deve conter o nome totalmente qualificado do conjunto do manipulador de protocolo sustado que é usado pelo Azure AD B2C para insights de aplicativos:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra o perfil técnico do Application Insights comum. Outros perfis técnicos do Application Insights incluem o AzureInsights-Common para alavancar sua configuração.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de reivindicações a serem enviadas ao Application Insights. Você também pode mapear o nome de sua reivindicação para um nome que você preferir aparecer no Application Insights. O exemplo a seguir mostra como enviar telemetrições para o Application Insights. As propriedades de um evento são `{property:NAME}`adicionadas através da sintaxe , onde NAME é propriedade sendo adicionada ao evento. DefaultValue pode ser um valor estático ou um valor que é resolvido por um dos [resolvedores](claim-resolver-overview.md)de sinistro suportados.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reivindicações de entrada ou gerar novas antes de enviar para o Application Insights.

## <a name="persist-claims"></a>Declarações de persistência

O elemento PersistedClaims não é usado.

## <a name="output-claims"></a>Declarações de saída

Os elementos OutputClaims e OutputClaimsTransformations não são usados.

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento CryptographicKeys não será usado.


## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| InstrumentationKey| Sim | A chave de [instrumentação](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)Application Insights, que será usada para registrar os eventos. | 
| DeveloperMode| Não | Um Boolean que indica se o modo desenvolvedor está ativado. Valores `true` possíveis: ou `false` (padrão). Esses metadados controlam como os eventos são protegidos. Em um ambiente de desenvolvimento com volume mínimo de eventos, permitir que o modo desenvolvedor resulte em eventos sendo enviados imediatamente ao Application Insights.|  
|Desativar Telemetria |Não |Um booleano que indica se a telemetria deve ser ativada ou não. Valores `true` possíveis: ou `false` (padrão).| 


## <a name="next-steps"></a>Próximas etapas

- [Crie um recurso de insights de aplicativos](../azure-monitor/app/create-new-resource.md)
- Saiba como rastrear o [comportamento do usuário no Azure Active Directory B2C usando insights de aplicativos](analytics-with-application-insights.md)
