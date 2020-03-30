---
title: Suporte de avaliação da VMware no Azure Migrate
description: Saiba mais sobre o suporte para avaliação vmware vm com avaliação do servidor migração do Azure.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: e0172656d06075f89a7c3a06e8d4e9be94e6f5d0
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389300"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação de VMware 

Este artigo resume os pré-requisitos e os requisitos de suporte quando você avalia as VMMs vMware para migração para o Azure, usando a ferramenta Azure Migrate:Server Assessment](migração-services-overview.md#azure-migrate-server-assessment-tool). Se você quiser migrar vMs VMware para o Azure, revise a [matriz de suporte à migração](migrate-support-matrix-vmware-migration.md).

Para avaliar as VMs do VMware, crie um projeto do Azure Migrate e adicione a ferramenta Avaliação do Servidor ao projeto. Depois que a ferramenta é adicionada, você implanta o [aparelho Azure Migrate](migrate-appliance.md). O aparelho descobre continuamente máquinas no local e envia metadados da máquina e dados de desempenho para o Azure. Depois que a descoberta é concluída, você reúne máquinas descobertas em grupos e executa uma avaliação para um grupo.

## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure.<br/><br/> Você pode descobrir e avaliar até 35.000 VMs VMware em um único [projeto](migrate-support-matrix.md#azure-migrate-projects). Um projeto também pode incluir servidores físicos e VMs Hyper-V, até os limites de avaliação para cada um.
**Descoberta** | O aparelho Azure Migrate pode descobrir até 10.000 VMs VMware em um vCenter Server.
**Avaliação** | Você pode adicionar até 35.000 máquinas em um único grupo.<br/><br/> Você pode avaliar até 35.000 VMs em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.


## <a name="application-discovery"></a>Descoberta de aplicativo

Além de descobrir máquinas, o Server Assessment pode descobrir aplicativos, papéis e recursos em execução em máquinas. Descobrir o inventário do aplicativo permite identificar e planejar um caminho de migração adaptado para suas cargas de trabalho no local. 

**Suporte** | **Detalhes**
--- | ---
**Máquinas suportadas** | A descoberta de aplicativos é suportada apenas para VMs VMware.
**Descoberta** | A descoberta de aplicativos é sem agente. Ele usa credenciais de hóspedes de máquinas e acessa remotamente máquinas usando chamadas WMI e SSH.
**Suporte a VM** | A descoberta de aplicativos é suportada para todas as versões do Windows e Linux.
**Credenciais do vCenter** | A descoberta do aplicativo precisa de uma conta vCenter Server com acesso somente leitura e privilégios habilitados para máquinas virtuais > operações de hóspedes.
**Credenciais de VM** | Atualmente, a descoberta de aplicativos suporta o uso de uma credencial para todos os servidores Windows e uma credencial para todos os servidores Linux.<br/><br/> Você cria uma conta de usuário convidado para VMs Windows e uma conta de usuário normal/normal (acesso não sudo) para todas as VMs Linux.
**Ferramentas VMware** | As ferramentas VMware devem ser instaladas e em execução em VMs que você deseja descobrir. <br/> A versão de ferramentas VMware deve ser superior a 10.2.0.
**Powershell** | As VMs devem ter a versão 2.0 ou posterior do PowerShell instalada.
**Acesso portuário** | Nos hosts ESXi que executam VMs que você deseja descobrir, o aparelho Azure Migrate deve ser capaz de se conectar à porta TCP 443.
**Limites** | Para a descoberta de aplicativos, você pode descobrir até 10000 VMs em cada aparelho Azure Migrate.



## <a name="vmware-requirements"></a>Requisitos da VMware

**Vmware** | **Detalhes**
--- | ---
**VMs VMware** | A avaliação é suportada para todos os sistemas operacionais Windows e Linux.
**servidor vCenter** | As máquinas que você deseja desembasar e avaliar devem ser gerenciadas pelo vCenter Server versão 5.5, 6.0, 6.5 ou 6.7.
**Permissões (avaliação)** | conta somente leitura do vCenter Server.
**Permissões (detecção de aplicativos)** | conta do vCenter Server com acesso somente leitura e privilégios habilitados para **máquinas virtuais > Operações de Hóspedes**.
**Permissões (visualização de dependência)** | Conta do Servidor Central com acesso somente leitura e privilégios habilitados para operações**de hóspedes** **de máquinas** > virtuais .


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

O Azure Migrate usa o [aparelho Azure Migrate](migrate-appliance.md) para descoberta e avaliação. Você pode implantar o aparelho como um VMWare VM usando um modelo OVA, importado para o vCenter Server ou usando um [script PowerShell](deploy-appliance-script.md).

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---vmware) para VMware.
- Saiba mais sobre [URLs](migrate-appliance.md#url-access) que o aparelho precisa acessar.

## <a name="port-access"></a>Acesso portuário

**Dispositivo** | **Conexão**
--- | ---
Dispositivos | Conexões de entrada na porta TCP 3389 para permitir conexões remotas de desktop ao aparelho.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento do aparelho usando a URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Conexões de saída na porta 443 (HTTPS), para enviar metadados de detecção e desempenho para o Azure Migrate.
Servidor vCenter | Conexões de entrada na porta TCP 443 para permitir que o aparelho colete metadados de configuração e desempenho para avaliações. <br/><br/> O aparelho se conecta ao vCenter na porta 443 por padrão. Se o servidor vCenter ouvir em uma porta diferente, você poderá modificar a porta quando configurar a descoberta.
Hosts ESXi (análise de detecção de aplicativos/dependência sem agente) | Se você quiser fazer a [detecção de aplicativos](how-to-discover-applications.md) ou [a análise de dependência sem agente,](concepts-dependency-visualization.md#agentless-analysis)então o aparelho se conecta aos hosts ESXi na porta TCP 443, para descobrir aplicativos, para e executar visualização de dependência sem agente em VMs.

## <a name="agentless-dependency-analysis-requirements"></a>Requisitos de análise de dependência sem agente

[A análise de dependência](concepts-dependency-visualization.md) ajuda você a identificar dependências entre máquinas locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência sem agente. 

**Exigência** | **Detalhes**
--- | --- 
**Antes da implantação** | Você deve ter um projeto Azure Migrate em vigor, com a ferramenta Avaliação do servidor adicionada ao projeto.<br/><br/>  Você implanta visualização de dependência depois de configurar um aparelho Azure Migrate para descobrir suas máquinas VMWare no local.<br/><br/> [Aprenda a](create-manage-projects.md) criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> [Saiba como](how-to-set-up-appliance-vmware.md) configurar o aparelho Azure Migrate para avaliação de VMs VMware.
**Suporte a VM** | Atualmente suportado apenas para VMs VMware.
**VMs Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits).
**Conta do Windows** |  Para análise de dependência, o aparelho Azure Migrate precisa de uma conta local ou de um administrador de domínio para acessar as VMs do Windows.
**VMs do Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Conta Linux** | Para análise de dependência, em máquinas Linux o aparelho Azure Migrate precisa de uma conta de usuário com privilégio Root.<br/><br/> Alternativamente, a conta de usuário precisa dessas permissões nos arquivos /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agentes necessários** | Nenhum agente necessário em máquinas que você queira analisar.
**Ferramentas VMware** | As ferramentas VMware (mais de 10.2) devem ser instaladas e em execução em cada VM que você deseja analisar.
**Credenciais do vCenter Server** | A visualização de dependência precisa de uma conta vCenter Server com acesso somente leitura e privilégios habilitados para máquinas virtuais > operações de hóspedes. 
**Powershell** | As VMs devem ter a versão 2.0 ou superior do PowerShell instalada.
**Acesso portuário** | Nos hosts ESXi que executam VMs que você deseja analisar, o aparelho Azure Migrate deve ser capaz de se conectar à porta TCP 443.

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise de dependência baseados em agentes

[A análise de dependência](concepts-dependency-visualization.md) ajuda você a identificar dependências entre máquinas locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agentes. 

**Exigência** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto Azure Migrate em vigor, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto.<br/><br/>  Você implanta visualização de dependência depois de configurar um aparelho Azure Migrate para descobrir suas máquinas no local<br/><br/> [Aprenda a](create-manage-projects.md) criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o aparelho Azure Migrate para avaliação de [servidores hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou físicos.
**Azure Governamental** | A visualização de dependência não está disponível no governo Azure.
**Análise de log** | O Azure Migrate usa a solução [Mapa de Serviço](../operations-management-suite/operations-management-suite-service-map.md) nos registros do Monitor do [Azure](../log-analytics/log-analytics-overview.md) para visualização de dependência.<br/><br/> Você associa um espaço de trabalho novo ou existente do Log Analytics a um projeto do Azure Migrate. O espaço de trabalho para um projeto do Azure Migrate não pode ser modificado depois de adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura do projeto Azure Migrate.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou Europa Ocidental. Espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região na qual [o Mapa de Serviço é suportado](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate é marcado com a tecla Migration Project e o nome do projeto.
**Agentes necessários** | Em cada máquina que deseja analisar, instale os seguintes agentes:<br/><br/> O [agente de monitoramento da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [agente de dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se as máquinas locais não estão conectadas à internet, você precisa baixar e instalar o gateway Log Analytics neles.<br/><br/> Saiba mais sobre a instalação do [agente de dependência](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura do projeto Azure Migrate.<br/><br/> O Azure Migrate suporta espaços de trabalho residentes nas regiões leste dos EUA, sudeste da Ásia e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar em uma região na qual [o Mapa de Serviço é suportado](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> O espaço de trabalho para um projeto do Azure Migrate não pode ser modificado depois de adicionado.
**Custos** | A solução Mapa de Serviço não incorre em nenhuma cobrança pelos primeiros 180 dias (a partir do dia em que você associa o espaço de trabalho do Log Analytics ao projeto Azure Migrate)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> O uso de qualquer solução que não seja o Mapa de Serviço no espaço de trabalho log analytics associado incorrerá em [taxas padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa de Serviço não é gratuito e cada nó será cobrado de acordo com o nível pago do espaço de trabalho do Log Analytics/<br/><br/>Se você tiver projetos que você criou antes do Azure Migrate disponibilidade geral (GA- 28 de fevereiro de 2018), você pode ter incorrido em taxas adicionais do Mapa de Serviço. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes da GA ainda são cobrados.
**Gerenciamento** | Quando você registra agentes no espaço de trabalho, você usa o ID e a chave fornecidas pelo projeto Azure Migrate.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto Azure Migrate associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado pelo Azure Migrate, a menos que você exclua o projeto Azure Migrate. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se as máquinas não estão conectadas à internet, você precisa instalar o gateway Log Analytics neles.


## <a name="next-steps"></a>Próximas etapas

- [Revise](best-practices-assessment.md) as melhores práticas para criar avaliações.
- [Prepare-se para avaliação de VMware.](tutorial-prepare-vmware.md)
