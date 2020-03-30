---
title: Filtragem baseada em inteligência de ameaças do Azure Firewall
description: A filtragem contra ameaças baseada em inteligência pode ser habilitada para o seu firewall de forma a alertar e rejeitar o tráfego de/para endereços IP e domínios mal-intencionados.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168663"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtragem baseada em inteligência baseada em ameaças do Azure Firewall

A filtragem contra ameaças baseada em inteligência pode ser habilitada para o seu firewall de forma a alertar e rejeitar o tráfego de/para endereços IP e domínios mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O [Grafo de Segurança Inteligente](https://www.microsoft.com/en-us/security/operations/intelligence) potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.

![Inteligência de ameaças de firewall](media/threat-intel/firewall-threat.png)

Se você habilitou a filtragem baseada em inteligência de ameaças, as regras associadas serão processadas antes de qualquer uma das regras do NAT, regras de rede ou regras de aplicativo.

Você pode optar por apenas registrar um alerta quando uma regra é acionada, ou você pode escolher o modo alerta e negar.

Por padrão, a filtragem baseada em inteligência de ameaças é ativada no modo de alerta. Você não pode desativar esse recurso ou alterar o modo até que a interface do portal esteja disponível na sua região.

![Interface de portal de filtragem baseada em inteligência de ameaças](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Logs

O trecho de log a seguir mostra uma regra desencadeada:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testes

- **Testes de saída** - Os alertas de tráfego de saída devem ser uma ocorrência rara, pois significa que seu ambiente foi comprometido. Para ajudar a testar os alertas de saída, foi criado um teste FQDN que dispara um alerta. Use **testmaliciousdomain.eastus.cloudapp.azure.com** para seus testes de saída.

- **Teste de entrada** - Você pode esperar ver alertas sobre o tráfego de entrada se as regras de DNAT estiverem configuradas no firewall. Isso é verdade, mesmo que apenas fontes específicas sejam permitidas na regra do DNAT e o tráfego seja negado de outra forma. O Azure Firewall não alerta todos os scanners de portas conhecidos; apenas em scanners que são conhecidos por também se envolverem em atividades maliciosas.

## <a name="next-steps"></a>Próximas etapas

- Veja [as amostras do Azure Firewall Log Analytics](log-analytics-samples.md)
- Saiba como [implantar e configurar um Firewall Azure](tutorial-firewall-deploy-portal.md)
- Revise o [relatório de inteligência da Microsoft Security](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)