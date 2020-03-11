---
title: Visualização de dependência em Migrações para Azure
description: Fornece uma visão geral dos cálculos de avaliação no serviço de avaliação do servidor nas migrações para Azure
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: fd069ed98fa34fd6f281c98a061925f96c7bb2cd
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082626"
---
# <a name="dependency-visualization"></a>Visualização de dependência

Este artigo descreve a visualização de dependência em migrações para Azure: avaliação de servidor.

## <a name="what-is-dependency-visualization"></a>O que é a visualização de dependência?

A visualização de dependência ajuda a identificar as dependências entre as máquinas locais que você deseja avaliar e migrar para o Azure. 

- Em migrações para Azure: avaliação de servidor, você coleta computadores em um grupo e, em seguida, avalia o grupo. A visualização de dependência ajuda a agrupar máquinas com mais precisão, com alta confiança para avaliação.
- A visualização de dependências permite que você identifique os computadores que devem ser migrados juntos. Você pode identificar se as máquinas estão em uso ou se elas podem ser descomissionadas em vez de migradas.
- A visualização de dependências ajuda a garantir que nada seja deixado para trás e evitar interrupções surpresa durante a migração.
- A visualização é especialmente útil se você não tiver certeza se as máquinas fazem parte de uma implantação de aplicativo que você deseja migrar para o Azure.


> [!NOTE]
> A visualização de dependência não está disponível no Azure governamental.

## <a name="agent-basedagentless-visualization"></a>Visualização baseada em agente/sem agente

Há duas opções para implantar a visualização de dependência:

- **Baseado em agente**: a visualização de dependência baseada em agente requer que os agentes sejam instalados em cada computador local que você deseja analisar.
- **Sem agente**: com essa opção, não é necessário instalar agentes em computadores que você deseja verificar de verificação cruzada. Essa opção está atualmente em visualização e só está disponível para VMs VMware.


## <a name="agent-based-visualization"></a>Visualização baseada em agente

**Requisito** | **Detalhes** | **Saiba mais**
--- | --- | ---
**Antes da implantação** | Você deve ter um projeto de migrações para Azure em vigor, com a ferramenta migrações para Azure: Server Assessment adicionada ao projeto.<br/><br/>  Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir seus computadores locais. | [Saiba como](create-manage-projects.md) criar um projeto pela primeira vez.<br/><br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/><br/> Saiba como configurar o dispositivo de migrações para Azure para avaliação de servidores [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)ou físicos.
**Agentes necessários** | Em cada computador que você deseja analisar, instale os seguintes agentes:<br/><br/> O [MMA (Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/><br/> O [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se os computadores locais não estiverem conectados à Internet, você precisará baixar e instalar Log Analytics gateway neles. | Saiba mais sobre como instalar o [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e o [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics** | As migrações para Azure usam a solução [mapa do serviço](../operations-management-suite/operations-management-suite-service-map.md) em [logs de Azure monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.<br/><br/> Você associa um espaço de trabalho Log Analytics novo ou existente a um projeto de migrações para Azure. O espaço de trabalho para um projeto de migrações para Azure não pode ser modificado após ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura que o projeto de migrações para Azure.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste asiático ou Europa Ocidental. Espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região na qual [mapa do serviço tem suporte](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> No Log Analytics, o espaço de trabalho associado à migração do Azure é marcado com a chave do projeto de migração e o nome do projeto.
**Com** | A solução de Mapa do Serviço não incorrerá em cobranças pelos primeiros 180 dias (a partir do dia em que você associa o espaço de trabalho Log Analytics com o projeto de migrações para Azure)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> Usar qualquer solução que não seja Mapa do Serviço no espaço de trabalho Log Analytics associado incorrerá em [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para log Analytics.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Depois de excluir o projeto, Mapa do Serviço uso não é gratuito e cada nó será cobrado de acordo com a camada paga de Log Analytics espaço de trabalho/<br/><br/>Se você tiver projetos criados antes de migrar a disponibilidade geral do Azure (GA-28 de fevereiro de 2018), você poderá ter incorrido Mapa do Serviço encargos adicionais. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes do GA ainda são passíveis de cobrança.
**Gerenciamento** | Ao registrar agentes no espaço de trabalho, você usa a ID e a chave fornecidas pelo projeto de migrações para Azure.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto de migrações do Azure associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado pela migração do Azure, a menos que você exclua o projeto de migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.

## <a name="agentless-visualization"></a>Visualização sem agente


**Requisito** | **Detalhes** | **Saiba mais**
--- | --- | ---
**Antes da implantação** | Você deve ter um projeto de migrações para Azure em vigor, com a ferramenta migrações para Azure: Server Assessment adicionada ao projeto.<br/><br/>  Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir suas máquinas VMWare locais. | [Saiba como](create-manage-projects.md) criar um projeto pela primeira vez.<br/><br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/><br/> Saiba como configurar o dispositivo de migrações para Azure para avaliação de VMs [VMware](how-to-set-up-appliance-vmware.md) .
**Agentes necessários** | Nenhum agente é necessário nos computadores que você deseja analisar.
**Sistemas operacionais com suporte** | Examine os [sistemas operacionais](migrate-support-matrix-vmware.md#agentless-dependency-visualization) com suporte para a visualização sem agente.
**VMs** | **Ferramentas do VMware**: as ferramentas do VMware devem ser instaladas e executadas em VMs que você deseja analisar.<br/><br/> **Conta**: no dispositivo de migrações para Azure, você precisa adicionar uma conta de usuário que possa ser usada para acessar VMs para análise.<br/><br/> **VMs do Windows**: a conta de usuário precisa ser um administrador local ou de domínio no computador.<br/><br/> **VMs do Linux**: o privilégio raiz é necessário na conta. Como alternativa, a conta de usuário requer esses dois recursos em arquivos/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE. | [Saiba mais sobre](migrate-appliance.md) o dispositivo migrações para Azure.
**VMware** | **vCenter**: o dispositivo precisa de uma conta de vCenter Server com acesso somente leitura e privilégios habilitados para máquinas virtuais > operações de convidado.<br/><br/> **Hosts ESXi**: em hosts ESXi executando VMs que você deseja analisar, o dispositivo migrações para Azure deve ser capaz de se conectar à porta TCP 443.
**Dados coletados** |  A análise de dependência sem agente funciona capturando dados de conexão TCP de computadores nos quais ele está habilitado. Depois que a descoberta de dependência é habilitada, o dispositivo coleta dados de conexão TCP a cada 5 minutos de VMs convidadas. Esses dados são coletados de VMs convidadas por meio de vCenter Server usando APIs vSphere. Os dados coletados são processados no dispositivo para deduzir as informações de dependência e enviadas para migrações para o Azure a cada 6 horas. Os dados a seguir são coletados de cada computador: <br/> -Nomes de processos que têm conexões ativas.<br/> -Nomes de aplicativos que executam o processo com conexões ativas.<br/> -Porta de destino nas conexões ativas.


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
- [Configurar a visualização de dependência](how-to-create-group-machine-dependencies.md)
- [Experimente a visualização de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md) para VMs VMware.
- Examine as [perguntas comuns](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre a visualização de dependência.


