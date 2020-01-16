---
title: Visão geral dos logs e das métricas do firewall do Azure
description: Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 1267b3295762f6eb6af92b1cec909bae768886c1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974518"
---
# <a name="azure-firewall-logs-and-metrics"></a>Logs e métricas do Firewall do Azure

Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.

Você pode acessar alguns desses logs por meio do portal. Os logs podem ser enviados para os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), o Armazenamento e os Hubs de Eventos e analisados nos logs do Azure Monitor ou por ferramentas diferentes, como Excel e Power BI.

As métricas são leves e podem dar suporte a cenários quase em tempo real, tornando-as úteis para alertas e detecção rápida de problemas.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

 Os seguintes logs de diagnóstico estão disponíveis para o Firewall do Azure:

* **Log de regra de aplicativo**

   O log de regras de aplicativo é salvo em uma conta de armazenamento, transmitida para os hubs de eventos e/ou enviados para Azure Monitor logs somente se você o tiver habilitado para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de aplicativo configurado gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Log de regra de rede**

   O log de regras de rede é salvo em uma conta de armazenamento, transmitida para os hubs de eventos e/ou enviados para Azure Monitor logs somente se você o tiver habilitado para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de rede configuradas gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Você tem três opções para armazenar os logs:

* **Conta de armazenamento**: as contas de armazenamento são mais adequadas para os logs quando eles são armazenados por mais tempo e examinados quando necessário.
* **Hubs de eventos**: os hubs de eventos são uma ótima opção para integração a outras ferramentas SEIM (informações de segurança e gerenciamento de evento) para receber alertas sobre os recursos.
* **Logs de Azure monitor**: os logs de Azure monitor são mais bem usados para o monitoramento geral em tempo real de seu aplicativo ou a análise de tendências.

## <a name="activity-logs"></a>Logs de atividade

   As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure.

   Você pode usar [os logs de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md) (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações enviadas à sua assinatura do Azure.

## <a name="metrics"></a>Métricas

As métricas em Azure Monitor são valores numéricos que descrevem algum aspecto de um sistema em um determinado momento. As métricas são coletadas a cada minuto e são úteis para alertas porque podem ser amostradas com frequência. Um alerta pode ser acionado rapidamente com lógica relativamente simples.

As seguintes métricas estão disponíveis para o Firewall do Azure:

- **Contagem de ocorrências de regras de aplicativo** -o número de vezes que uma regra de aplicativo foi atingida.

    Unidade: contagem

- **Contagem de ocorrências de regras de rede** -o número de vezes que uma regra de rede foi atingida.

    Unidade: contagem

- **Dados processados** -quantidade de dados atravessando o firewall.

    Unidade: bytes

- **Estado de integridade do firewall** – indica a integridade do firewall.

    Unidade: porcentagem

   Essa métrica tem duas dimensões:
  - **Status**: os valores possíveis estão *íntegros*, *degradados*, não *íntegros*.
  - **Motivo**: indica o motivo do status correspondente do firewall. Por exemplo, ele pode indicar *portas SNAT* se o status do firewall for degradado ou não íntegro.





- **Utilização da porta SNAT** -a porcentagem de portas SNAT que foram utilizadas pelo firewall.

    Unidade: porcentagem

   Quando você adiciona mais endereços IP públicos ao firewall, mais portas SNAT estão disponíveis, reduzindo a utilização de portas SNAT. Além disso, quando o firewall é dimensionado por diferentes motivos (por exemplo, CPU ou taxa de transferência) portas SNAT adicionais também ficam disponíveis. De modo eficaz, um determinado percentual de utilização de portas SNAT pode ficar inativo sem a adição de endereços IP públicos, apenas porque o serviço foi expandido. Você pode controlar diretamente o número de endereços IP públicos disponíveis para aumentar as portas disponíveis no firewall. Mas, você não pode controlar diretamente o dimensionamento de firewall. Atualmente, as portas SNAT são adicionadas somente aos cinco primeiros endereços IP públicos.   


## <a name="next-steps"></a>Próximos passos

- Para saber como monitorar as métricas e logs do Firewall do Azure, veja [Tutorial: Monitorar os logs de Firewall do Azure](tutorial-diagnostics.md).

- Para saber mais sobre as métricas em Azure Monitor, consulte [métricas em Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
