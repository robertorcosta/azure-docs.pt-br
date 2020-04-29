---
title: Suporte para avaliação do Hyper-V em migrações para Azure
description: Saiba mais sobre o suporte à avaliação do Hyper-V com a avaliação do servidor de migrações para Azure
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 990d5026d9621c144c31635fabac4416eb9d20e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538164"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matriz de suporte para avaliação do Hyper-V

Este artigo resume os pré-requisitos e requisitos de suporte ao avaliar as VMs do Hyper-V para migração para o Azure, usando a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Se você quiser migrar VMs do Hyper-V para o Azure, examine a [matriz de suporte de migração](migrate-support-matrix-hyper-v-migration.md).

Para configurar a avaliação de VM do Hyper-V, você cria um projeto de migrações para Azure e adiciona a ferramenta de avaliação do servidor ao projeto. Depois que a ferramenta for adicionada, você implantará o [dispositivo migrações para Azure](migrate-appliance.md). O dispositivo descobre continuamente computadores locais e envia metadados de computador e dados de desempenho para o Azure. Após a conclusão da descoberta, você coleta computadores descobertos em grupos e executa uma avaliação de um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 VMs do Hyper-V em um único [projeto de migrações para Azure](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Além das VMs do Hyper-V, um projeto pode incluir VMs VMware e servidores físicos, até os limites de avaliação de cada um.
**Descoberta** | O dispositivo de migrações para Azure pode descobrir até 5000 VMs do Hyper-V.<br/><br/> O dispositivo pode se conectar a até 300 hosts Hyper-V.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> Você pode avaliar até 35.000 VMs em uma única avaliação para um grupo.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.



## <a name="hyper-v-host-requirements"></a>Requisitos de host do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Host Hyper-V**       | O host Hyper-V pode ser autônomo ou implantado em um cluster.<br/><br/> O host Hyper-V pode executar o Windows Server 2019, o Windows Server 2016 ou o Windows Server 2012 R2.<br/> Não é possível avaliar as VMs localizadas em hosts Hyper-V que executam o Windows Server 2012.
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. <br/> Se você não quiser atribuir permissões de administrador, crie uma conta de usuário local ou de domínio e adicione a conta de usuário a esses grupos – usuários de gerenciamento remoto, administradores do Hyper-V e usuários de monitor de desempenho. |
| **Comunicação remota do PowerShell**   | A [comunicação remota do PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) deve ser habilitada em cada host Hyper-V. |
| **Réplica do Hyper-V**       | Se você usar a réplica do Hyper-V (ou se tiver várias VMs com os mesmos identificadores de VM) e descobrir as VMs originais e replicadas usando as migrações para Azure, a avaliação gerada pelas migrações para Azure pode não ser precisa. |


## <a name="hyper-v-vm-requirements"></a>Requisitos de VM do Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) . |
| **Serviços de Integração**       | Os [Integration Services do Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar em execução em VMs que você avaliar, a fim de capturar informações do sistema operacional. |


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

As migrações para Azure usam o [dispositivo de migrações para Azure](migrate-appliance.md) para descoberta e avaliação. Você pode implantar o dispositivo usando um VHD do Hyper-V compactado que você baixa do portal ou usando um [script do PowerShell](deploy-appliance-script.md).

- Saiba mais sobre [os requisitos de dispositivo](migrate-appliance.md#appliance---hyper-v) para o Hyper-V.
- Saiba mais sobre URLs que o dispositivo precisa acessar em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls) .
- No Azure governamental, você deve implantar o dispositivo usando o script.

## <a name="port-access"></a>Acesso à porta

A tabela a seguir resume os requisitos de porta para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Baseado** | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Conexões de saída nas portas 443 (HTTPS), para enviar metadados de descoberta e desempenho para migrações para Azure.
**Host/cluster do Hyper-V** | Conexões de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS) para efetuar pull de metadados e dados de desempenho para VMs do Hyper-V usando uma sessão modelo CIM (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise de dependência baseada em agente

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre computadores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agente. Atualmente, o Hyper-V dá suporte apenas à visualização de dependências baseadas em agente. 

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto de migrações para Azure em vigor, com a ferramenta de avaliação do servidor adicionada ao projeto.<br/><br/>  Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir seus computadores locais<br/><br/> [Saiba como](create-manage-projects.md) criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o dispositivo de migrações para Azure para avaliação de [VMs do Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Governamental** | A visualização de dependência não está disponível no Azure governamental.
**Log Analytics** | As migrações para Azure usam a solução [mapa do serviço](../operations-management-suite/operations-management-suite-service-map.md) em [logs de Azure monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.<br/><br/> Você associa um espaço de trabalho Log Analytics novo ou existente a um projeto de migrações para Azure. O espaço de trabalho para um projeto de migrações para Azure não pode ser modificado após ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura que o projeto de migrações para Azure.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste asiático ou Europa Ocidental. Espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região na qual [mapa do serviço tem suporte](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> No Log Analytics, o espaço de trabalho associado à migração do Azure é marcado com a chave do projeto de migração e o nome do projeto.
**Agentes necessários** | Em cada computador que você deseja analisar, instale os seguintes agentes:<br/><br/> O [MMA (Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se os computadores locais não estiverem conectados à Internet, você precisará baixar e instalar Log Analytics gateway neles.<br/><br/> Saiba mais sobre como instalar o [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e o [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto de migrações para Azure.<br/><br/> As migrações para Azure dão suporte a espaços de trabalho que residem nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar em uma região na qual [mapa do serviço tem suporte](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> O espaço de trabalho para um projeto de migrações para Azure não pode ser modificado após ser adicionado.
**Com** | A solução de Mapa do Serviço não incorrerá em cobranças pelos primeiros 180 dias (a partir do dia em que você associa o espaço de trabalho Log Analytics com o projeto de migrações para Azure)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> Usar qualquer solução que não seja Mapa do Serviço no espaço de trabalho Log Analytics associado incorrerá em [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para log Analytics.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Depois de excluir o projeto, Mapa do Serviço uso não é gratuito e cada nó será cobrado de acordo com a camada paga de Log Analytics espaço de trabalho/<br/><br/>Se você tiver projetos criados antes de migrar a disponibilidade geral do Azure (GA-28 de fevereiro de 2018), você poderá ter incorrido Mapa do Serviço encargos adicionais. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes do GA ainda são passíveis de cobrança.
**Gerenciamento** | Ao registrar agentes no espaço de trabalho, você usa a ID e a chave fornecidas pelo projeto de migrações para Azure.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto de migrações do Azure associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado pela migração do Azure, a menos que você exclua o projeto de migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se os computadores não estiverem conectados à Internet, você precisará instalar o Log Analytics gateway neles.
**Azure Governamental** | Não há suporte para a análise de dependência baseada em agente.

## <a name="next-steps"></a>Próximas etapas

[Preparar a avaliação de VM do Hyper-V](tutorial-prepare-hyper-v.md)
