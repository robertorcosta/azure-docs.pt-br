---
title: Análise de dependência na avaliação do servidor azure migrate
description: Descreve como usar a análise de dependência para avaliação usando a avaliação do servidor Azure Migrate.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f96496b66d6bcfd397fb0a7303d3dbfb4fd6f6b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455631"
---
# <a name="dependency-analysis"></a>Análise de dependência

Este artigo descreve a análise de dependência no Azure Migrate:Server Assessment.

## <a name="overview"></a>Visão geral

A análise de dependência ajuda você a identificar dependências entre máquinas locais que você deseja avaliar e migrar para o Azure. 

- No Azure Migrate:Server Assessment, você reúne máquinas em um grupo e, em seguida, avalia o grupo. A análise de dependência ajuda você a agrupar máquinas com mais precisão, com alta confiança para avaliação.
- A análise de dependência permite identificar máquinas que devem ser migradas juntas. Você pode identificar se as máquinas estão em uso ou se elas podem ser desativadas em vez de migradas.
- Analisar dependências ajuda a garantir que nada fique para trás e evitar paralisações surpresadurante a migração.
- A análise é especialmente útil se você não tiver certeza se as máquinas fazem parte de uma implantação de aplicativo que você deseja migrar para o Azure.
- [Revise](common-questions-discovery-assessment.md#what-is-dependency-visualization) perguntas comuns sobre análise de dependência.

Existem duas opções para implantar análise de dependência

- **Análise de**dependência baseada em agente : A análise de dependência baseada em agentes requer que os agentes sejam instalados em cada máquina no local que você deseja analisar.
- **Sem agente**: Com a análise sem agente, você não precisa instalar agentes em máquinas que deseja cruzar. Esta opção está atualmente em pré-visualização, e está disponível apenas para VMware VMware.

> [!NOTE]
> A análise de dependência não está disponível no governo Azure.

## <a name="agentless-analysis"></a>Análise sem agente

A análise de dependência sem agente funciona capturando dados de conexão TCP de máquinas para as quais está habilitado. Nenhum agente está instalado em máquinas que você deseja analisar.

### <a name="collected-data"></a>Dados coletados

Após o início da descoberta da dependência, o aparelho pesquisa dados de máquinas a cada cinco minutos para coletar dados. Esses dados são coletados de VMs convidados via vCenter Server, usando APIs vSphere. Os dados coletados são processados no aparelho Azure Migrate, para deduzir informações de identidade, e são enviados ao Azure Migrate a cada seis horas.

A pesquisa coleta esses dados das máquinas: 
- Nome de processos que possuem conexões ativas.
- Nome do aplicativo que executa processos que possuem conexões ativas.
- Porta de destino nas conexões ativas.

## <a name="agent-based-analysis"></a>Análise baseada em agentes

Para análise baseada em agentes, a Avaliação do Servidor usa a [solução Mapa de Serviço](../azure-monitor/insights/service-map.md) no Monitor do Azure para permitir a visualização e análise de dependência. O [agente Microsoft Monitoring Agent/Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) e o agente [dependency](../azure-monitor/platform/agents-overview.md#dependency-agent)devem ser instalados em cada máquina que você deseja analisar.

### <a name="collected-data"></a>Dados coletados

Para visualização baseada em agentes, os seguintes dados são coletados:

- Nome do servidor da máquina de origem, processo, nome do aplicativo.
- Nome do servidor da máquina de destino, processo, nome do aplicativo e porta.
- O número de conexões, informações de latência e transferência de dados são coletados e disponíveis para consultas do Log Analytics. 


## <a name="compare-agentless-and-agent-based"></a>Compare agente e agente baseado em agentes

As diferenças entre visualização sem agente e visualização baseada em agentes são resumidas na tabela.

**Exigência** | **Sem agente** | **Baseado em agentes**
--- | --- | ---
Suporte | Esta opção está atualmente em pré-visualização, e está disponível apenas para VMware VMware. [Revisar](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) sistemas operacionais suportados. | Disponibilidade geral (GA).
Agente | Não há necessidade de instalar agentes em máquinas que você deseja cruzar. | Agentes a serem instalados em cada máquina local que você deseja analisar: o [agente de monitoramento microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)e o agente de [dependência](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Não obrigatório. | O Azure Migrate usa a solução [Mapa de Serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nos registros do Monitor do [Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para análise de dependência. 
Como ele funciona | Captura dados de conexão TCP em máquinas habilitadas para visualização de dependência. Após a descoberta, ele coleta dados em intervalos de cinco minutos. | Os agentes do Mapa de Serviço instalados em uma máquina coletam dados sobre processos TCP e conexões de entrada/saída para cada processo.
Dados | Nome do servidor da máquina de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor da máquina de destino, processo, nome do aplicativo e porta. | Nome do servidor da máquina de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor da máquina de destino, processo, nome do aplicativo e porta.<br/><br/> O número de conexões, informações de latência e transferência de dados são coletados e disponíveis para consultas do Log Analytics. 
Visualização | O mapa de dependência de um único servidor pode ser visualizado durante uma hora a 30 dias. | Mapa de dependência de um único servidor.<br/><br/> O mapa pode ser visto em apenas uma hora.<br/><br/> Mapa de dependência de um grupo de servidores.<br/><br/> Adicione e remova servidores em um grupo da exibição do mapa.
Exportação de dados | Não pode ser baixado no momento em formato tabular. | Os dados podem ser consultados com o Log Analytics.



## <a name="next-steps"></a>Próximas etapas
- Revise os requisitos para configurar a análise baseada em agentes para [VMs VMware,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [servidores físicos](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)e [VMs Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Revise](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) os requisitos para análise sem agente de VMware VMs.
- [Configurar visualização](how-to-create-group-machine-dependencies.md) de dependência baseada em agentes
- [Experimente a](how-to-create-group-machine-dependencies-agentless.md) visualização de dependência sem agente para VMs VMware.
- Revise [questões comuns](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre visualização de dependência.


