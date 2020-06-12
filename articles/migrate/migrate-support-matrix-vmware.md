---
title: Suporte de avaliação do VMware nas Migrações para Azure
description: Saiba mais sobre o suporte para a avaliação de VM DO VMware com a Avaliação do Servidor de Migrações para Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 0ec7006ce240df8c6e07afffa886e78ca9bc2a4d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849358"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação do VMware 

Este artigo resume os pré-requisitos e requisitos de suporte ao avaliar as VMs do VMware para Migrações para Azure, usando a ferramenta [Migrações para Azure: Avaliação do Servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool). Se você quiser migrar as VMs do VMware para o Azure, examine a [matriz de suporte à migração](migrate-support-matrix-vmware-migration.md).

Para avaliar as VMs do VMware, crie um projeto de Migrações para Azure e adicione a ferramenta de Avaliação do Servidor ao projeto. Depois que a ferramenta for adicionada, implante o [dispositivo do Migrações para Azure](migrate-appliance.md). O dispositivo continuamente descobre computadores locais e envia metadados e dados de desempenho do computador para o Azure. Após concluir a descoberta, reúna os computadores descobertos em grupos e execute uma avaliação para um grupo.

## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure.<br/><br/> É possível descobrir e avaliar até 35.000 VMs do VMware em um único [projeto](migrate-support-matrix.md#azure-migrate-projects). Um projeto também pode incluir servidores físicos e VMs do Hyper-V, até os limites de avaliação de cada um.
**Descoberta** | O dispositivo de Migrações para Azure pode descobrir até 10.000 VMs do VMware em um vCenter Server.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> É possível avaliar até 35.000 VMs em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.


## <a name="application-discovery"></a>Descoberta de aplicativo

Além de descobrir os computadores, a Avaliação do Servidor pode descobrir aplicativos, funções e recursos em execução nos computadores. Descobrir o inventário de aplicativos permite identificar e planejar um caminho de migração adaptado para as cargas de trabalho locais. 

**Suporte** | **Detalhes**
--- | ---
**Computadores compatíveis** | No momento, a descoberta de aplicativos é compatível apenas com as VMs do VMware.
**Descoberta** | A descoberta de aplicativos é sem agente. Ele usa as credenciais de convidado do computador e acessa os computadores remotamente, usando chamadas de WMI e SSH.
**Suporte à VM** | A descoberta de aplicativos é compatível com todas as versões do Windows e Linux.
**Credenciais do vCenter** | A descoberta de aplicativos precisa de uma conta do vCenter Server com acesso somente leitura e privilégios habilitados para Máquinas Virtuais > Operações de Convidado.
**Credenciais da VM** | No momento, a descoberta de aplicativos é compatível com o uso de uma credencial para todos os servidores do Windows e uma credencial para todos os servidores do Linux.<br/><br/> Crie uma conta de usuário convidado para VMs do Windows e uma conta de usuário regular/normal (acesso não sudo) para todas as VMs do Linux.
**Ferramentas do VMware** | As ferramentas do VMware devem ser instaladas e executadas nas VMs que você deseja descobrir. <br/> A versão das ferramentas do VMware deve ser posterior à 10.2.0.
**PowerShell** | As VMs do Windows devem ter o PowerShell versão 2.0 ou posterior instalado.
**Acesso à porta** | Nos hosts ESXi que executam as VMs que você deseja descobrir, o dispositivo de Migrações para Azure deve ser conectado à porta TCP 443.
**Limites** | Para a descoberta de aplicativos, você pode descobrir até 10.000 VMs em cada dispositivo de Migrações para Azure.



## <a name="vmware-requirements"></a>Requisitos do VMware

**VMware** | **Detalhes**
--- | ---
**VMs VMware** | A avaliação é compatível com todos os sistemas operacionais Windows e Linux.
**vCenter Server** | Os computadores que você deseja descobrir devem ser gerenciados por um vCenter Server versão 5.5, 6.0, 6.5 ou 6.7.
**Permissões (avaliação)** | Conta somente leitura do vCenter Server.
**Permissões (descoberta de aplicativos)** | Conta do vCenter Server com acesso somente leitura e privilégios habilitados para **Máquinas Virtuais > Operações de Convidado**.
**Permissões (visualização de dependência)** | Conta do vCenter Server com acesso somente leitura e privilégios habilitados para **Máquinas virtuais** > **Operações de convidado**.


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

As Migrações para Azure usam o dispositivo das [Migrações para Azure](migrate-appliance.md) para descoberta e avaliação. Você pode implantar o dispositivo como uma VM do VMWare usando um modelo OVA, importado para o vCenter Server, ou usando um [script do PowerShell](deploy-appliance-script.md).

- Saiba mais sobre os [requisitos de dispositivo](migrate-appliance.md#appliance---vmware) para VMware.
- Saiba mais sobre as URLs do Azure que o dispositivo precisa acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
- No Azure Governamental, você deve implantar o dispositivo [usando um script](deploy-appliance-script-government.md).


## <a name="port-access"></a>Acesso à porta

**Dispositivo** | **Conexão**
--- | ---
Dispositivo | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivos usando a URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Conexões de saída na porta 443 (HTTPS) para enviar metadados de descoberta e desempenho para as Migrações para Azure.
Servidor vCenter | Conexões de entrada na porta TCP 443 para permitir que o dispositivo colete metadados de configuração e desempenho para avaliações. <br/><br/> O dispositivo é conectado ao vCenter na porta 443 por padrão. Se o vCenter Server for detectado em uma porta diferente, você pode modificar a porta ao configurar a descoberta.
Hosts ESXi (descoberta de aplicativo/análise de dependência sem agente) | Se você quiser realizar a [descoberta de aplicativos](how-to-discover-applications.md) ou [análise de dependência sem agente](concepts-dependency-visualization.md#agentless-analysis), o dispositivo será conectado aos hosts ESXi na porta TCP 443 para descobrir aplicativos e executar a visualização de dependência sem agente nas VMs.

## <a name="application-discovery"></a>Descoberta de aplicativo

Além de descobrir os computadores, a Avaliação do Servidor pode descobrir aplicativos, funções e recursos em execução nos computadores. Descobrir o inventário de aplicativos permite identificar e planejar um caminho de migração adaptado para as cargas de trabalho locais. 

**Suporte** | **Detalhes**
--- | ---
**Computadores compatíveis** | No momento, a descoberta de aplicativos é compatível apenas com as VMs do VMware.
**Descoberta** | A descoberta de aplicativos é sem agente. Ele usa as credenciais de convidado do computador e acessa os computadores remotamente, usando chamadas de WMI e SSH.
**Suporte à VM** | A descoberta de aplicativos é compatível com todas as versões do Windows e Linux.
**Credenciais do vCenter** | A descoberta de aplicativos precisa de uma conta do vCenter Server com acesso somente leitura e privilégios habilitados para Máquinas Virtuais > Operações de Convidado.
**Credenciais da VM** | No momento, a descoberta de aplicativos é compatível com o uso de uma credencial para todos os servidores do Windows e uma credencial para todos os servidores do Linux.<br/><br/> Crie uma conta de usuário convidado para VMs do Windows e uma conta de usuário regular/normal (acesso não sudo) para todas as VMs do Linux.
**Ferramentas do VMware** | As ferramentas do VMware devem ser instaladas e executadas nas VMs que você deseja descobrir. <br/> A versão das ferramentas do VMware deve ser posterior à 10.2.0.
**PowerShell** | As VMs devem ter o PowerShell versão 2.0 ou posterior instalado.
**Acesso à porta** | Nos hosts ESXi que executam as VMs que você deseja descobrir, o dispositivo de Migrações para Azure deve ser conectado à porta TCP 443.
**Limites** | Para a descoberta de aplicativos, você pode descobrir até 10.000 VMs em cada dispositivo de Migrações para Azure.


## <a name="agentless-dependency-analysis-requirements"></a>Requisitos da análise de dependência sem agente

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre os computadores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência sem agente. 

**Requisito** | **Detalhes**
--- | --- 
**Antes da implantação** | Você deve ter um projeto de Migrações para Azure em vigor, com a ferramenta de Avaliação do Servidor adicionada ao projeto.<br/><br/>  Implante a visualização de dependência depois de configurar um dispositivo de Migrações para Azure para descobrir os computadores locais do VMWare.<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/> [Aprenda](how-to-set-up-appliance-vmware.md) a configurar o dispositivo de Migrações para Azure para avaliação das VMs do VMware.
**Suporte à VM** | No momento, compatível apenas com as VMs do VMware.
**VMs do Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits).
**Conta do Windows** |  Para a análise de dependência, o dispositivo de Migrações para Azure precisa de uma conta de administrador de domínio ou de uma conta de administrador local para acessar as VMs do Windows.
**VMs do Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Conta do Linux** | Para análise de dependência, nos computadores Linux, o dispositivo de Migrações para Azure precisa de uma conta de usuário com privilégio de Raiz.<br/><br/> Como alternativa, a conta de usuário precisa dessas permissões nos arquivos /bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agentes necessários** | Nenhum agente é necessário nos computadores que você deseja analisar.
**Ferramentas do VMware** | As ferramentas do VMware (posterior à 10.2) devem ser instaladas e executadas em cada VM que você deseja analisar.
**Credenciais do vCenter Server** | A visualização de dependência precisa de uma conta do vCenter Server com acesso somente leitura e privilégios habilitados para Máquinas Virtuais > Operações de Convidado. 
**PowerShell** | As VMs do Windows devem ter o PowerShell versão 2.0 ou posterior instalado.
**Acesso à porta** | Nos hosts ESXi que executam as VMs que você deseja analisar, o dispositivo de Migrações para Azure deve ser conectado à porta TCP 443.


## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos da análise de dependência baseada em agente

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre os computadores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agente. 

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto de Migrações para Azure em vigor, com as Migrações para Azure: Ferramenta de avaliação do servidor adicionada ao projeto.<br/><br/>  Implante a visualização de dependência depois de configurar um dispositivo de Migrações para Azure para descobrir os computadores locais<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/> Aprenda a configurar o dispositivo de Migrações para Azure para avaliação de [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) ou servidores físicos.
**Azure Governamental** | A visualização de dependência não está disponível no Azure Governamental.
**Log Analytics** | As Migrações para Azure usam a solução [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md) nos [logs do Azure Monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.<br/><br/> Associe um workspace do Log Analytics novo ou existente a um projeto de Migrações para Azure. Não é possível modificar o espaço de trabalho de um projeto de Migrações para Azure depois que ele foi adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura que o projeto de Migrações para Azure.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou oeste da Europa. Os espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> No Log Analytics, o espaço de trabalho associado às Migrações para Azure é marcado com a chave do Projeto de Migração e o nome do projeto.
**Agentes necessários** | Em cada computador que você deseja analisar, instale os seguintes agentes:<br/><br/> O [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se os computadores locais não estiverem conectados com a Internet, será necessário baixar e instalar o gateway do Log Analytics.<br/><br/> Saiba mais sobre a instalação do [agente de dependência](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de Trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto de Migrações para Azure.<br/><br/> As Migrações para Azure são compatível com os espaços de trabalho que residem nas regiões leste dos EUA, sudeste da Ásia e oeste da Europa.<br/><br/>  O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Não é possível modificar o espaço de trabalho de um projeto de Migrações para Azure depois que ele foi adicionado.
**Custos** | A solução Mapa do Serviço não gera encargos nos primeiros 180 dias (desde o dia da associação do workspace do Log Analytics com o projeto de Migrações para Azure)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> O uso de qualquer solução que não seja o Mapa do Serviço no workspace do Log Analytics associado gerará [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics Standard.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa do Serviço não será mais gratuito e cada nó será cobrado de acordo com a camada paga do workspace do Log Analytics/<br/><br/>Se você tiver projetos criados antes da disponibilidade geral de Migrações para Azure (disponibilidade geral - 28 de fevereiro de 2018), pode ter gerado encargos adicionais do Mapa do Serviço. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes da disponibilidade geral ainda são passíveis de cobrança.
**Gerenciamento** | Ao registrar agentes no espaço de trabalho, use a ID e a chave fornecidas pelo projeto de Migrações para Azure.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto de Migrações para Azure associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado por Migrações para Azure, a menos que exclua o projeto de Migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se os computadores não estiverem conectados com a Internet, será necessário instalar o gateway do Log Analytics.
**Azure Governamental** | A análise de dependência baseada em agente não é compatível.


## <a name="next-steps"></a>Próximas etapas

- [Examine](best-practices-assessment.md) as melhores práticas para a criação de avaliações.
- [Prepare-se para a avaliação do VMware](tutorial-prepare-vmware.md).
