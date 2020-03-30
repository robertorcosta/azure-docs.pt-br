---
title: Monitore contêineres com registros do Monitor Azure
description: Use registros do Monitor do Azure para monitorar contêineres em execução em clusters de malha de serviço do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614427"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitore contêineres com registros do Monitor Azure
 
Este artigo abrange as etapas necessárias para configurar a solução de monitoramento de contêineres do Azure Monitor para visualizar eventos de contêineres. Para configurar o cluster para coletar eventos de contêiner, consulte [tutorial passo a passo](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurar a solução de monitoramento de contêineres

> [!NOTE]
> Você precisa ter os logs do Monitor do Azure configurados para o cluster, bem como ter o agente Log Analytics implantado em seus nós. Se não o fizer, siga as etapas em [Configurar os logs do Monitor Do Azure](service-fabric-diagnostics-oms-setup.md) e [adicione o agente Log Analytics a um cluster](service-fabric-diagnostics-oms-agent.md) primeiro.

1. Uma vez que o cluster esteja configurado com os registros do Monitor do Azure e com o agente Log Analytics, implante seus contêineres. Aguarde até que os contêineres sejam implantados antes de passar para a próxima etapa.

2. No Azure Marketplace, pesquise *Solução de monitoramento de contêiner* e clique no recurso **Solução de monitoramento de contêiner** que aparecerá abaixo da categoria Monitoramento + Gerenciamento.

    ![Adicionando a solução de Contêineres](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro do mesmo workspace que já foi criado para o cluster. Essa alteração automaticamente aciona o agente para iniciar a coleta de dados de docker nos contêineres. Em cerca de 15 minutos, você verá a solução se acender com os logs e estatísticas de entrada, como mostra a imagem abaixo.

    ![Painel de análise de Log básica](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente permite a coleta de vários registros específicos de contêineres que podem ser consultados em logs do Monitor Azure ou usados para visualizar indicadores de desempenho. Os tipos de log coletados são:

* ContainerInventory: mostra informações sobre imagens, nome e localização do contêiner
* ContainerImageInventory: informações sobre imagens implantadas, inclusive IDs ou tamanhos
* ContainerLog: logs de erros específicos, logs de docker (stdout etc.) e outras entradas
* ContainerServiceLog: comandos de daemon do docker que foram executados
* Perf: contadores de desempenho incluindo o CPU, memória, tráfego de rede, E/S do disco e métricas personalizadas das máquinas de host do contêiner



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [a solução de contêineres de logs do Azure Monitor](../azure-monitor/insights/containers.md).
* Leia mais sobre a orquestração de contêineres no Service Fabric: [Service Fabric e contêineres](service-fabric-containers-overview.md)
* Familiarize-se com os recursos de [pesquisa e consulta de log](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs do Monitor do Azure
* Configure os logs do Monitor do Azure para configurar regras [de alerta automatizadas](../log-analytics/log-analytics-alerts.md) para ajudar na detecção e diagnóstico