---
title: Personalize as regras usando o CLI - Azure Web Application Firewall
description: Este artigo fornece informações sobre como personalizar as regras do Firewall de Aplicativos da Web no Application Gateway com o Cli do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048526"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Personalize as regras do Firewall de Aplicativos da Web usando o Azure CLI

O WAF (Azure Application Gateway Web Application Firewall, firewall de aplicativos web) do Azure Application oferece proteção para aplicativos web. Essas proteções são fornecidas pelo CRS (conjunto de regras principais) do OWASP (Open Web Application Security Project). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o Gateway de Aplicativo possibilita que a capacidade personalize regras e grupos de regras. Para obter mais informações sobre os grupos e regras de regras específicas, consulte [Lista de grupos e regras do CRS do Firewall de Aplicativos da Web](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Exibir grupos de regras e regras

O exemplo de código a seguir mostra como exibir regras e grupos de regras que podem ser configurados.

### <a name="view-rule-groups"></a>Exibir grupos de regras

O exemplo abaixo mostra como exibir os grupos de regras:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

A saída a seguir é uma resposta truncada do exemplo anterior:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Exibir regras em um grupo de regras

O exemplo a seguir mostra como exibir as regras em um grupo de regras especificado:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

A saída a seguir é uma resposta truncada do exemplo anterior:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Desabilitar regras

O exemplo a seguir desabilita as regras `910018` e `910017` em um gateway de aplicativo:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que fazem com que o WAF bloqueie a solicitação enquanto estiver no modo de prevenção (no modo de detecção eles são registrados como exceções). Estes não podem ser configurados ou desativados:

* A não análise do órgão de solicitação resulta no bloqueio da solicitação, a menos que a inspeção corporal seja desligada (XML, JSON, dados de formulário)
* O comprimento dos dados do corpo de solicitação (sem arquivos) é maior do que o limite configurado
* O corpo de solicitação (incluindo arquivos) é maior do que o limite
* Um erro interno aconteceu no motor WAF

CRS 3.x específico:

* Pontuação de anomalia de entrada excedeu o limite

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as regras desabilitadas, você pode aprender como exibir os logs de WAF. Para obter mais informações, consulte [os diagnósticos do Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
