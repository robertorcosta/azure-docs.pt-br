---
title: Monitoramento de localizações de dados no Azure Monitor | Microsoft Docs
description: Descreve os diferentes locais onde os dados de monitoramento são armazenados no Azure, incluindo a plataforma de dados Do Monitor do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666608"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorando os dados no Azure Monitor

O Azure Monitor é baseado em uma plataforma de [dados](data-platform.md) de [Logs](data-platform-logs.md) e [Metrics,](data-platform-metrics.md) conforme descrito na [plataforma de dados Do Azure Monitor](data-platform.md). Os dados de monitoramento dos recursos do Azure podem ser gravados em outros locais, porém, antes de serem copiados para o Azure Monitor ou para suportar cenários adicionais. 

## <a name="monitoring-data-locations"></a>Monitoramento de localizações de dados

A tabela a seguir identifica os diferentes locais onde os dados de monitoramento no Azure são enviados e os diferentes métodos para acessá-los.

| Location | Descrição | Métodos de acesso |
|:---|:---|:---|:--|
| Métricas do Monitor Azure | Banco de dados de séries tempois otimizados para análise de dados carimbados. | [Explorador de métricas](metrics-getting-started.md)<br>[API de métricas do Monitor do Azure](/rest/api/monitor/metrics) |
| Logs do Azure Monitor    | O espaço de trabalho do Log Analytics é baseado no Azure Data Explorer, que fornece um poderoso mecanismo de análise e uma linguagem de consulta rica. | [Análise de log](../log-query/portals.md)<br>[API de análise de log](https://dev.loganalytics.io/)<br>[API do Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Log de atividades | Os dados do registro de atividades são mais úteis quando enviados ao Azure Monitor Logs para analisá-los com outros dados, mas também são coletados por conta própria para que possam ser visualizados diretamente no portal do Azure. | [Portal Azure](activity-log-view.md#azure-portal)<br>[API de Eventos do Azure Monitor](/rest/api/monitor/eventcategories) |
| Armazenamento do Azure | Algumas fontes de dados gravarão diretamente no armazenamento do Azure e exigirão a configuração para mover dados para Logs. Você também pode enviar dados para o armazenamento do Azure para arquivamento e para integração com sistemas externos.  | [Análise de Armazenamento](/rest/api/storageservices/storage-analytics)<br>[Gerenciador de Servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Gerenciador de Armazenamento](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Hubs de Eventos | Envie dados para o Azure Event Hubs para transmiti-los para outros locais. | [Captura para armazenamento](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor para VMs | O Azure Monitor para VMs armazena dados de saúde da carga de trabalho em um local personalizado que é usado por sua experiência de monitoramento no portal Azure. | [Portal Azure](../insights/vminsights-overview.md)<br>[API DE RESTO do monitor de carga de trabalho](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[API de saúde de recursos do Azure REST](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alertas | Alertas criados pelo Azure Monitor. | [Portal Azure](alerts-managing-alert-instances.md)<br>[API de gerenciamento de alertas REST](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Próximas etapas

- Veja as diferentes fontes de dados de [monitoramento coletadas pelo Azure Monitor](data-sources.md).
- Conheça os [tipos de dados de monitoramento coletados pelo Azure Monitor](data-platform.md) e como visualizar e analisar esses dados.
