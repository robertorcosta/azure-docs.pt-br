---
title: Configurar o monitoramento de PV com insights de contêiner | Microsoft Docs
description: Este artigo descreve como você pode configurar o monitoramento de clusters kubernetes com volumes persistentes com informações de contêiner.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713721"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Configurar o monitoramento de PV com informações de contêiner

A partir do Agent versão *ciprod10052020*, o agente integrado do insights de contêineres agora dá suporte ao monitoramento do uso de PV (volume persistente).

## <a name="pv-metrics"></a>Métricas de PV

O insights de contêiner inicia automaticamente o monitoramento VP coletando as seguintes métricas em intervalos de 60sec e armazenando-as na tabela **InsightMetrics** .

|Nome da métrica |Dimensão métrica (marcas) |Descrição |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Espaço usado em bytes para um volume persistente específico com uma declaração usada por um pod específico. `pvCapacityBytes` é dobrado como uma dimensão no campo marcas para reduzir o custo de ingestão de dados e simplificar as consultas.|

## <a name="monitor-persistent-volumes"></a>Monitorar volumes persistentes

As informações de contêiner incluem gráficos pré-configurados para essa métrica em uma pasta de trabalho para cada cluster. Você pode encontrar os gráficos na guia volume persistente da pasta de trabalho **detalhes da carga** de trabalho diretamente de um cluster AKs selecionando pastas de trabalho no painel esquerdo e na lista suspensa **exibir pastas de trabalho** no Insight. Você também pode habilitar um alerta recomendado para uso de PV, bem como consultar essas métricas em Log Analytics.  

![Exemplo de pasta de trabalho do PV de Azure Monitor](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as métricas de PV coletadas [aqui](./container-insights-agent-config.md).
