---
title: Gerencie alertas do System Center Operations Manager, Zabbix e Nagios no Azure Monitor
description: Gerencie alertas do System Center Operations Manager, Zabbix e Nagios no Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 9fbad920bdfa26419e4b865db5d4e53dad97acce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539441"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Gerencie alertas do System Center Operations Manager, Zabbix e Nagios no Azure Monitor

Agora você pode exibir os alertas do Nagios, do Zabbix e do System Center Operations Manager no [Azure Monitor](https://aka.ms/azure-alerts-overview). Esses alertas provenientes de integrações com servidores Nagios/Zabbix ou o System Center Operations Manager para o Log Analytics. 

## <a name="prerequisites"></a>Pré-requisitos
Os registros do repositório do Log Analytics com um tipo de Alerta serão importados para o Azure Monitor e, portanto, você precisará executar a configuração necessária para coletar esses registros.
1. Para alertas de **Nagios** e **Zabbix** , [Configure esses servidores](../learn/quick-collect-linux-computer.md) para [enviar alertas](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) para log Analytics.
1. Para **System Center Operations Manager** alertas, [Conecte seu grupo de gerenciamento de Operations Manager ao seu espaço de trabalho do log Analytics](./om-agents.md). Depois disso, implante a solução [Gerenciamento de Alertas](./alert-management-solution.md) por meio do marketplace de soluções do Azure. Quando você terminar, todos os alertas criados no System Center Operations Manager serão importados para o Log Analytics.

## <a name="view-your-alert-instances"></a>Exibir suas instâncias de alerta
Depois de configurar a importação para o Log Analytics, comece a exibir instâncias de alerta desses serviços de monitoramento no [Azure Monitor](https://aka.ms/azure-alerts-overview). Quando eles estiverem presentes no Azure Monitor, você poderá [gerenciar suas instâncias de alerta](https://aka.ms/managing-alert-instances), [gerenciar grupos inteligentes criados nesses alertas](https://aka.ms/managing-smart-groups) e [alterar o estado dos alertas e dos grupos inteligentes](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Essa solução só permite que você exiba as instâncias de alerta System Center Operations Manager/Zabbix/Nagios acionadas no Azure Monitor. A configuração de regra de alerta só pode ser exibida/editada nas respectivas ferramentas de monitoramento. 
>  1. Todas as instâncias de alerta disparadas estarão disponíveis no Azure Monitor e no Azure Log Analytics. Atualmente, não há nenhuma maneira de escolher entre os dois ou ingerir somente os alertas disparados específicos.
>  1. Todos os alertas de System Center Operations Manager, Zabbix e Nagios têm o tipo de sinal "Unknown", pois o tipo de telemetria subjacente não está disponível.
>  1. Os alertas do Nagios não têm estado – por exemplo, a [condição de monitoramento](https://aka.ms/azure-alerts-overview) de um alerta não mudará de "Disparado" para "Resolvido". Em vez disso, "Disparado" e "Resolvido" são exibidos como instâncias de alerta separadas. 
