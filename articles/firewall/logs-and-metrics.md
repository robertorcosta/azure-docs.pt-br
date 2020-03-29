---
title: Visão geral dos logs e métricas do Firewall do Azure
description: Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315024"
---
# <a name="azure-firewall-logs-and-metrics"></a>Logs e métricas do Firewall do Azure

Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.

Você pode acessar alguns desses logs por meio do portal. Os logs podem ser enviados para os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), o Armazenamento e os Hubs de Eventos e analisados nos logs do Azure Monitor ou por ferramentas diferentes, como Excel e Power BI.

As métricas são leves e podem suportar cenários próximos em tempo real, tornando-os úteis para alertar e detectar problemas rápidos.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

 Os seguintes logs de diagnóstico estão disponíveis para o Firewall do Azure:

* **Log de regra de aplicativo**

   O registro de regra do aplicativo é salvo em uma conta de armazenamento, transmitido para hubs de eventos e/ou enviado para logs do Azure Monitor somente se você o habilitou para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de aplicativo configurado gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

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

   O registro de regras da rede é salvo em uma conta de armazenamento, transmitido para hubs de eventos e/ou enviado para logs do Azure Monitor somente se você o habilitou para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de rede configuradas gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

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
* **Registros do Monitor do Azure**: Os registros do Monitor do Azure são mais usados para monitoramento geral em tempo real do seu aplicativo ou para olhar as tendências.

## <a name="activity-logs"></a>Logs de atividade

   As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure.

   Você pode usar [registros de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md) (anteriormente conhecidos como logs operacionais e registros de auditoria) para visualizar todas as operações submetidas à sua assinatura do Azure.

## <a name="metrics"></a>Métricas

Métricas no Monitor Azure são valores numéricos que descrevem algum aspecto de um sistema em um determinado momento. As métricas são coletadas a cada minuto e são úteis para alertar, pois podem ser amostradas com freqüência. Um alerta pode ser disparado rapidamente com lógica relativamente simples.

As seguintes métricas estão disponíveis para o Firewall Do Azure:

- **Regras de aplicação contagem** regressiva - O número de vezes que uma regra de aplicativo foi atingida.

    Unidade: contagem

- **Contagem de rebatidas das regras** da rede - O número de vezes que uma regra de rede foi atingida.

    Unidade: contagem

- **Dados processados** - Quantidade de dados atravessando o firewall.

    Unidade: bytes

- **Estado de saúde do firewall** - Indica a saúde do firewall com base na disponibilidade da porta SNAT.

    Unidade: percentual

   Esta métrica tem duas dimensões:
  - Estado: Os valores possíveis são *saudáveis,* *degradados,* *insalubres.*
  - Motivo: Indica o motivo do status correspondente do firewall. 

     Se as portas SNAT forem utilizadas > 95%, elas são consideradas esgotadas e a saúde é de 50% com status=**Degradada** e razão=**Porta SNAT**. O firewall continua processando o tráfego e as conexões existentes não são afetadas. No entanto, novas conexões podem não ser estabelecidas intermitentemente.

     Se as portas SNAT forem usadas < 95%, o firewall é considerado saudável e a saúde é mostrada como 100%.

     Se não for relatado o uso de portas SNAT, a saúde será mostrada como 0%. 

- **Utilização da porta SNAT** - A porcentagem de portas SNAT que foram utilizadas pelo firewall.

    Unidade: percentual

   Quando você adiciona mais endereços IP públicos ao seu firewall, mais portas SNAT estão disponíveis, reduzindo a utilização das portas SNAT. Além disso, quando o firewall é dimensionado por diferentes razões (por exemplo, CPU ou throughput) portas SNAT adicionais também ficam disponíveis. Assim, efetivamente, uma determinada porcentagem de utilização de portas SNAT pode cair sem que você adicione endereços IP públicos, apenas porque o serviço foi dimensionado. Você pode controlar diretamente o número de endereços IP públicos disponíveis para aumentar as portas disponíveis em seu firewall. Mas, você não pode controlar diretamente o firewall de escala. Atualmente, as portas SNAT são adicionadas apenas para os cinco primeiros endereços IP públicos.   


## <a name="next-steps"></a>Próximas etapas

- Para saber como monitorar as métricas e logs do Firewall do Azure, veja [Tutorial: Monitorar os logs de Firewall do Azure](tutorial-diagnostics.md).

- Para saber mais sobre métricas no Azure Monitor, consulte [Métricas no Monitor Do Azure](../azure-monitor/platform/data-platform-metrics.md).
