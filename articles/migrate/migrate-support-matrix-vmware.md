---
title: Suporte de avaliação do VMware nas migrações para Azure
description: Saiba mais sobre o suporte para a avaliação de VM VMware com a avaliação de servidor migrações para Azure.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8a09562f14b95256ee9c2b5ba7d9c308cde66397
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532197"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação do VMware 

Este artigo resume os pré-requisitos e requisitos de suporte ao avaliar as VMs do VMware para migração para o Azure, usando a ferramenta migrações para Azure: Server Assessment] (migrate-Services-Overview. MD # Azure-Migrate-Server-Assessment-Tool). Se você quiser migrar VMs VMware para o Azure, examine a [matriz de suporte de migração](migrate-support-matrix-vmware-migration.md).

Para avaliar as VMs do VMware, você cria um projeto de migrações para Azure e, em seguida, adiciona a ferramenta de avaliação do servidor ao projeto. Depois que a ferramenta for adicionada, você implantará o [dispositivo migrações para Azure](migrate-appliance.md). O dispositivo descobre continuamente computadores locais e envia metadados de computador e dados de desempenho para o Azure. Após a conclusão da descoberta, você coleta computadores descobertos em grupos e executa uma avaliação de um grupo.

## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure.<br/><br/> Você pode descobrir e avaliar até 35.000 VMs VMware em um único [projeto](migrate-support-matrix.md#azure-migrate-projects). Um projeto também pode incluir servidores físicos e VMs do Hyper-V, até os limites de avaliação de cada um.
**Descoberta** | O dispositivo de migrações para Azure pode descobrir até 10.000 VMs VMware em um vCenter Server.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> Você pode avaliar até 35.000 VMs em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.


## <a name="application-discovery"></a>Descoberta de aplicativo

Além de descobrir as máquinas, a avaliação do servidor pode descobrir aplicativos, funções e recursos em execução em computadores. Descobrir seu inventário de aplicativos permite que você identifique e planeje um caminho de migração adaptado para suas cargas de trabalho locais. 

**Suporte** | **Detalhes**
--- | ---
**Computadores com suporte** | Atualmente, o app Discovery tem suporte apenas para VMs VMware.
**Descoberta** | A descoberta de aplicativos é sem agente. Ele usa credenciais de convidado de computador e acessa remotamente computadores usando chamadas de WMI e SSH.
**Suporte de VM** | A descoberta de aplicativo tem suporte para todas as versões do Windows e Linux.
**credenciais do vCenter** | A descoberta de aplicativos precisa de uma conta de vCenter Server com acesso somente leitura e os privilégios habilitados para máquinas virtuais > operações de convidado.
**Credenciais da VM** | Atualmente, a descoberta de aplicativos dá suporte ao uso de uma credencial para todos os servidores Windows e uma credencial para todos os servidores Linux.<br/><br/> Você cria uma conta de usuário convidado para VMs do Windows e uma conta de usuário regular/normal (acesso não sudo) para todas as VMs do Linux.
**Ferramentas do VMware** | As ferramentas do VMware devem ser instaladas e executadas em VMs que você deseja descobrir. <br/> A versão das ferramentas do VMware deve ser posterior à 10.2.0.
**PowerShell** | As VMs devem ter o PowerShell versão 2,0 ou posterior instalado.
**Acesso à porta** | Em hosts ESXi que executam VMs que você deseja descobrir, o dispositivo de migrações para Azure deve ser capaz de se conectar à porta TCP 443.
**limites** | Para a descoberta de aplicativos, você pode descobrir até 10000 VMs em cada dispositivo de migrações para Azure.



## <a name="vmware-requirements"></a>Requisitos da VMware

**VMware** | **Detalhes**
--- | ---
**VMs VMware** | A avaliação tem suporte para todos os sistemas operacionais Windows e Linux.
**vCenter Server** | Os computadores que você deseja descobrir e avaliar devem ser gerenciados por vCenter Server versão 5,5, 6,0, 6,5 ou 6,7.
**Permissões (avaliação)** | vCenter Server conta somente leitura.
**Permissões (descoberta de aplicativo)** | vCenter Server conta com acesso somente leitura e privilégios habilitados para **máquinas virtuais > operações de convidado**.
**Permissões (visualização de dependência)** | Conta do servidor central com acesso somente leitura e privilégios habilitados para > **operações de convidado**de **máquinas virtuais**.


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

As migrações para Azure usam o [dispositivo de migrações para Azure](migrate-appliance.md) para descoberta e avaliação. Você pode implantar o dispositivo como uma VM VMWare usando um modelo OVA, importado para vCenter Server ou usando um [script do PowerShell](deploy-appliance-script.md).

- Saiba mais sobre [os requisitos de dispositivo](migrate-appliance.md#appliance---vmware) para VMware.
- Saiba mais sobre URLs que o dispositivo precisa acessar em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls) .
- No Azure governamental, você deve implantar o dispositivo usando o script.


## <a name="port-access"></a>Acesso à porta

**Dispositivo** | **Conexão**
--- | ---
Dispositivos | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Conexões de saída na porta 443 (HTTPS), para enviar metadados de descoberta e desempenho para migrações para Azure.
Servidor vCenter | Conexões de entrada na porta TCP 443 para permitir que o dispositivo colete metadados de configuração e desempenho para avaliações. <br/><br/> O dispositivo se conecta ao vCenter na porta 443 por padrão. Se o servidor vCenter escutar em uma porta diferente, você poderá modificar a porta ao configurar a descoberta.
Hosts ESXi (análise de dependência sem agente/descoberta de aplicativo) | Se você quiser fazer a [descoberta de aplicativo](how-to-discover-applications.md) ou a [análise de dependência sem agente](concepts-dependency-visualization.md#agentless-analysis), o dispositivo se conecta aos hosts ESXi na porta TCP 443, para descobrir aplicativos e executar a visualização de dependência sem agente em VMS.

## <a name="agentless-dependency-analysis-requirements"></a>Requisitos de análise de dependência sem agente

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre computadores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência sem agente. 

**Requisito** | **Detalhes**
--- | --- 
**Antes da implantação** | Você deve ter um projeto de migrações para Azure em vigor, com a ferramenta de avaliação do servidor adicionada ao projeto.<br/><br/>  Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir suas máquinas VMWare locais.<br/><br/> [Saiba como](create-manage-projects.md) criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> [Saiba como](how-to-set-up-appliance-vmware.md) configurar o dispositivo de migrações para Azure para avaliação de VMs VMware.
**Suporte de VM** | Atualmente com suporte somente para VMs VMware.
**VMs Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits).
**Conta do Windows** |  Para a análise de dependência, o dispositivo de migrações para Azure precisa de uma conta local ou de administrador de domínio para acessar as VMs do Windows.
**VMs do Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14, 4, 16, 4<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Conta do Linux** | Para análise de dependência, em computadores Linux, o dispositivo de migrações para Azure precisa de uma conta de usuário com privilégio de raiz.<br/><br/> Como alternativa, a conta de usuário precisa dessas permissões em arquivos/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agentes necessários** | Nenhum agente é necessário nos computadores que você deseja analisar.
**Ferramentas do VMware** | As ferramentas do VMware (posteriores a 10,2) devem ser instaladas e executadas em cada VM que você deseja analisar.
**vCenter Server credenciais** | A visualização de dependência precisa de uma conta vCenter Server com acesso somente leitura e privilégios habilitados para máquinas virtuais > operações de convidado. 
**PowerShell** | As VMs devem ter o PowerShell versão 2,0 ou superior instalado.
**Acesso à porta** | Em hosts ESXi que executam VMs que você deseja analisar, o dispositivo de migrações para Azure deve ser capaz de se conectar à porta TCP 443.


## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise de dependência baseada em agente

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre computadores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agente. 

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto de migrações para Azure em vigor, com a ferramenta migrações para Azure: Server Assessment adicionada ao projeto.<br/><br/>  Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir seus computadores locais<br/><br/> [Saiba como](create-manage-projects.md) criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o dispositivo de migrações para Azure para avaliação de servidores [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)ou físicos.
**Azure Governamental** | A visualização de dependência não está disponível no Azure governamental.
**Log Analytics** | As migrações para Azure usam a solução [mapa do serviço](../operations-management-suite/operations-management-suite-service-map.md) em [logs de Azure monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.<br/><br/> Você associa um espaço de trabalho Log Analytics novo ou existente a um projeto de migrações para Azure. O espaço de trabalho para um projeto de migrações para Azure não pode ser modificado após ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura que o projeto de migrações para Azure.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste asiático ou Europa Ocidental. Espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região na qual [mapa do serviço tem suporte](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> No Log Analytics, o espaço de trabalho associado à migração do Azure é marcado com a chave do projeto de migração e o nome do projeto.
**Agentes necessários** | Em cada computador que você deseja analisar, instale os seguintes agentes:<br/><br/> O [MMA (Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se os computadores locais não estiverem conectados à Internet, você precisará baixar e instalar Log Analytics gateway neles.<br/><br/> Saiba mais sobre como instalar o [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e o [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto de migrações para Azure.<br/><br/> As migrações para Azure dão suporte a espaços de trabalho que residem nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar em uma região na qual [mapa do serviço tem suporte](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> O espaço de trabalho para um projeto de migrações para Azure não pode ser modificado após ser adicionado.
**Com** | A solução de Mapa do Serviço não incorrerá em cobranças pelos primeiros 180 dias (a partir do dia em que você associa o espaço de trabalho Log Analytics com o projeto de migrações para Azure)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> Usar qualquer solução que não seja Mapa do Serviço no espaço de trabalho Log Analytics associado incorrerá em [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para log Analytics.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Depois de excluir o projeto, Mapa do Serviço uso não é gratuito e cada nó será cobrado de acordo com a camada paga de Log Analytics espaço de trabalho/<br/><br/>Se você tiver projetos criados antes de migrar a disponibilidade geral do Azure (GA-28 de fevereiro de 2018), você poderá ter incorrido Mapa do Serviço encargos adicionais. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes do GA ainda são passíveis de cobrança.
**Gerenciamento** | Ao registrar agentes no espaço de trabalho, você usa a ID e a chave fornecidas pelo projeto de migrações para Azure.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto de migrações do Azure associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado pela migração do Azure, a menos que você exclua o projeto de migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se os computadores não estiverem conectados à Internet, você precisará instalar o Log Analytics gateway neles.
**Azure Governamental** | Não há suporte para a análise de dependência baseada em agente.


## <a name="next-steps"></a>Próximas etapas

- [Examine](best-practices-assessment.md) as práticas recomendadas para a criação de avaliações.
- [Prepare-se para a avaliação do VMware](tutorial-prepare-vmware.md) .
