---
title: Definir um perfil técnico de Application Insights em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico Application Insights em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201405"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definir um perfil técnico de Application Insights em uma política personalizada de Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) dá suporte ao envio de dados de eventos diretamente para [Application insights](../azure-monitor/app/app-insights-overview.md) usando a chave de instrumentação fornecida para Azure ad B2C.  Com um perfil técnico Application Insights, você pode obter logs de eventos detalhados e personalizados para que seus percursos do usuário:

* Obter insights sobre o comportamento do usuário.
* Solucionar problemas de suas próprias políticas em desenvolvimento ou em produção.
* Medir o desempenho.
* Criar notificações do Application Insights.


## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **Handler** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo que é usado pelo Azure AD B2C para Application insights:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra o perfil técnico Application Insights comum. Outros perfis técnicos de Application Insights incluem o umconjunto-Common para aproveitar sua configuração.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas para Application insights. Você também pode mapear o nome da sua declaração para um nome que você prefira que apareça em Application Insights. O exemplo a seguir mostra como enviar telemetrias para Application Insights. As propriedades de um evento são adicionadas por meio da sintaxe `{property:NAME}` , em que Name é a propriedade que está sendo adicionada ao evento. DefaultValue pode ser um valor estático ou um valor que é resolvido por um dos [resolvedores de declaração](claim-resolver-overview.md)com suporte.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de enviar para Application insights.

## <a name="persist-claims"></a>Declarações de persistência

O elemento PersistedClaims não é usado.

## <a name="output-claims"></a>Declarações de saída

Os elementos OutputClaims e OutputClaimsTransformations não são usados.

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento CryptographicKeys não será usado.


## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| InstrumentationKey| Sim | A [chave de instrumentação](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)de Application insights, que será usada para registrar os eventos em log. | 
| DeveloperMode| Não | Um booliano que indica se o modo de desenvolvedor está habilitado. Valores possíveis: `true` ou `false` (padrão). Esses metadados controlam como os eventos são armazenados em buffer. Em um ambiente de desenvolvimento com volume mínimo de eventos, habilitar o modo de desenvolvedor resulta em eventos enviados imediatamente para Application Insights.|  
|DisableTelemetry |Não |Um booliano que indica se a telemetria deve ser habilitada ou não. Valores possíveis: `true` ou `false` (padrão).| 


## <a name="next-steps"></a>Próximas etapas

- [Criar um recurso do Application Insights](../azure-monitor/app/create-new-resource.md)
- Saiba como [acompanhar o comportamento do usuário no Azure Active Directory B2C usando Application insights](analytics-with-application-insights.md)
