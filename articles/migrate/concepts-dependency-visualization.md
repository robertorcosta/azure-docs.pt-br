---
title: Análise de dependência na avaliação do servidor de migrações para Azure
description: Descreve como usar a análise de dependência para avaliação usando a avaliação de servidor de migrações para Azure.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024754"
---
# <a name="dependency-analysis"></a>Análise de dependência

Este artigo descreve a análise de dependência em migrações para Azure: avaliação de servidor.

## <a name="overview"></a>Visão geral

A análise de dependência ajuda a identificar dependências entre computadores locais que você deseja avaliar e migrar para o Azure. 

- Em migrações para Azure: avaliação de servidor, você coleta computadores em um grupo e, em seguida, avalia o grupo. A análise de dependência ajuda a agrupar máquinas com mais precisão, com alta confiança para avaliação.
- A análise de dependência permite que você identifique os computadores que devem ser migrados juntos. Você pode identificar se as máquinas estão em uso ou se elas podem ser descomissionadas em vez de migradas.
- A análise de dependências ajuda a garantir que nada seja deixado para trás e evitar interrupções surpresa durante a migração.
- A análise será especialmente útil se você não tiver certeza se as máquinas fazem parte de uma implantação de aplicativo que você deseja migrar para o Azure.
- [Examine](common-questions-discovery-assessment.md#what-is-dependency-visualization) as perguntas comuns sobre a análise de dependência.

Há duas opções para implantar a análise de dependência

- **Baseado em agente**: a análise de dependência baseada em agente requer que os agentes sejam instalados em cada computador local que você deseja analisar.
- **Sem agente**: com a análise sem agente, não é necessário instalar agentes em computadores que você deseja verificar de verificação cruzada. Essa opção está atualmente em visualização e só está disponível para VMs VMware.

> [!NOTE]
> A análise de dependência baseada em agente não está disponível no Azure governamental. Você pode usar a análise de dependência sem agente.

## <a name="agentless-analysis"></a>Análise sem agente

A análise de dependência sem agente funciona capturando dados de conexão TCP de computadores para os quais ele está habilitado. Nenhum agente está instalado nos computadores que você deseja analisar.

### <a name="collected-data"></a>Dados coletados

Depois que a descoberta de dependência é iniciada, o dispositivo sonda dados de computadores a cada cinco minutos para coletar dados. Esses dados são coletados de VMs convidadas por meio de vCenter Server, usando APIs vSphere. Os dados coletados são processados no dispositivo de migrações para Azure, para deduzir informações de identidade e são enviados para migrações para o Azure a cada seis horas.

A sondagem coleta esses dados dos computadores: 
- Nome dos processos que têm conexões ativas.
- Nome do aplicativo que executa processos que têm conexões ativas.
- Porta de destino nas conexões ativas.

## <a name="agent-based-analysis"></a>Análise baseada em agente

Para análise baseada em agente, a avaliação do servidor usa a [solução mapa do serviço](../azure-monitor/insights/service-map.md) no Azure monitor para habilitar a visualização e a análise de dependência. O [agente Microsoft Monitoring Agent/log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) e o [agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent)devem ser instalados em cada computador que você deseja analisar.

### <a name="collected-data"></a>Dados coletados

Para a análise baseada em agente, os seguintes dados são coletados:

- Nome do servidor do computador de origem, processo, nome do aplicativo.
- Nome do servidor do computador de destino, processo, nome do aplicativo e porta.
- Número de conexões, latência e informações de transferência de dados são coletadas e disponibilizadas para consultas de Log Analytics. 


## <a name="compare-agentless-and-agent-based"></a>Compare sem agente e com base em agente

As diferenças entre a visualização sem agente e a visualização baseada em agente são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseado em agente**
--- | --- | ---
Suporte | Essa opção está atualmente em visualização e só está disponível para VMs VMware. [Examine](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) os sistemas operacionais com suporte. | Em disponibilidade geral (GA).
Agente | Não é necessário instalar agentes em computadores que você deseja verificar. | Agentes a serem instalados em cada computador local que você deseja analisar: o [MMA (Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)e o [Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Não obrigatório. | As migrações para Azure usam a solução [mapa do serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) em [logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para análise de dependência. 
Como isso funciona | Captura dados de conexão TCP em computadores habilitados para visualização de dependência. Após a descoberta, ele coleta dados em intervalos de cinco minutos. | Mapa do Serviço agentes instalados em um computador coletam dados sobre processos TCP e conexões de entrada/saída para cada processo.
Dados | Nome do servidor do computador de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor do computador de destino, processo, nome do aplicativo e porta. | Nome do servidor do computador de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor do computador de destino, processo, nome do aplicativo e porta.<br/><br/> Número de conexões, latência e informações de transferência de dados são coletadas e disponibilizadas para consultas de Log Analytics. 
Visualização | O mapa de dependências de um único servidor pode ser exibido durante uma duração de uma hora a 30 dias. | Mapa de dependências de um único servidor.<br/><br/> O mapa pode ser exibido somente em uma hora.<br/><br/> Mapa de dependências de um grupo de servidores.<br/><br/> Adicionar e remover servidores de um grupo da exibição de mapa.
Exportação de dados | Atualmente, não pode ser baixado no formato tabular. | Os dados podem ser consultados com Log Analytics.



## <a name="next-steps"></a>Próximas etapas
- Examine os requisitos para configurar a análise baseada em agente para [VMs VMware](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), [servidores físicos](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)e [VMs do Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Examine](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) os requisitos de análise sem agente de VMs VMware.
- [Configurar](how-to-create-group-machine-dependencies.md) a visualização de dependência baseada em agente
- [Experimente](how-to-create-group-machine-dependencies-agentless.md) a visualização de dependência sem agente para VMs VMware.
- Examine as [perguntas comuns](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre a visualização de dependência.


