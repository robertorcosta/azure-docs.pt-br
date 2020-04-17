---
title: Suporte para avaliação de Hiper-V no Azure Migrate
description: Saiba mais sobre o suporte para avaliação do Hyper-V com avaliação do servidor Azure Migrate
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 990d5026d9621c144c31635fabac4416eb9d20e6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538164"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matriz de suporte para avaliação de hiper-V

Este artigo resume os requisitos de pré-requisitos e suporte quando você avalia As VMs Hyper-V para migração para o Azure, usando a ferramenta [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Se você quiser migrar hiper-VVs para o Azure, revise a [matriz de suporte à migração](migrate-support-matrix-hyper-v-migration.md).

Para configurar a avaliação do Hyper-V V V, crie um projeto do Azure Migrate e adicione a ferramenta Avaliação do Servidor ao projeto. Depois que a ferramenta é adicionada, você implanta o [aparelho Azure Migrate](migrate-appliance.md). O aparelho descobre continuamente máquinas no local e envia metadados da máquina e dados de desempenho para o Azure. Depois que a descoberta é concluída, você reúne máquinas descobertas em grupos e executa uma avaliação para um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 Hiper-VMs em um único [projeto Azure Migrate](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Além dos VMs Hyper-V, um projeto pode incluir VMware VMs e servidores físicos, até os limites de avaliação para cada um.
**Descoberta** | O aparelho Azure Migrate pode descobrir até 5000 VMs Hyper-V.<br/><br/> O aparelho pode se conectar a até 300 hosts Hyper-V.
**Avaliação** | Você pode adicionar até 35.000 máquinas em um único grupo.<br/><br/> Você pode avaliar até 35.000 VMs em uma única avaliação para um grupo.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.



## <a name="hyper-v-host-requirements"></a>Requisitos de host Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Host Hyper-V**       | O host Hyper-V pode ser autônomo ou implantado em um cluster.<br/><br/> O host Hyper-V pode executar o Windows Server 2019, o Windows Server 2016 ou o Windows Server 2012 R2.<br/> Não é possível avaliar as VMs localizadas em hosts Hyper-V que executam o Windows Server 2012.
| **Permissões**           | Você precisa de permissões de administrador no host Hyper-V. <br/> Se você não quiser atribuir permissões de administrador, crie uma conta de usuário local ou de domínio e adicione a conta de usuário a esses grupos: Usuários de gerenciamento remoto, administradores hiper-v e usuários do monitor de desempenho. |
| **Comunicação remota do PowerShell**   | [O remoting PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) deve ser ativado em cada host Hyper-V. |
| **Réplica do Hyper-V**       | Se você usar a Réplica Hyper-V (ou tiver várias VMs com os mesmos identificadores de VM) e descobrir as VMs originais e replicadas usando o Azure Migrate, a avaliação gerada pelo Azure Migrate pode não ser precisa. |


## <a name="hyper-v-vm-requirements"></a>Requisitos do Hiper-V VM

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) |
| **Integration Services**       | [Os Serviços de Integração Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar sendo executados em VMs que você avalia, a fim de capturar informações do sistema operacional. |


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

O Azure Migrate usa o [aparelho Azure Migrate](migrate-appliance.md) para descoberta e avaliação. Você pode implantar o aparelho usando um Hyper-V VHD comprimido que você baixa do portal ou usando um [script PowerShell](deploy-appliance-script.md).

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---hyper-v) para Hyper-V.
- Saiba mais sobre URLs que o aparelho precisa acessar em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
- No Governo Azure, você deve implantar o aparelho usando o script.

## <a name="port-access"></a>Acesso portuário

A tabela a seguir resume os requisitos da porta para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Aparelho** | Conexões de entrada na porta TCP 3389 para permitir conexões remotas de desktop ao aparelho.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento do aparelho usando a URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Conexões de saída nas portas 443 (HTTPS), para enviar metadados de detecção e desempenho para o Azure Migrate.
**Host/cluster hiper-V** | Conexões de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS), para extrair metadados e dados de desempenho para Hiper-VMs usando uma sessão CIM (Common Information Model).

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise de dependência baseados em agentes

[A análise de dependência](concepts-dependency-visualization.md) ajuda você a identificar dependências entre máquinas locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agentes. Atualmente, o Hyper-V suporta apenas a visualização de dependência baseada em agentes. 

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto Azure Migrate em vigor, com a ferramenta Avaliação do servidor adicionada ao projeto.<br/><br/>  Você implanta visualização de dependência depois de configurar um aparelho Azure Migrate para descobrir suas máquinas no local<br/><br/> [Aprenda a](create-manage-projects.md) criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o aparelho Azure Migrate para avaliação de [VMs Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Governamental** | A visualização de dependência não está disponível no governo Azure.
**Log Analytics** | O Azure Migrate usa a solução [Mapa de Serviço](../operations-management-suite/operations-management-suite-service-map.md) nos registros do Monitor do [Azure](../log-analytics/log-analytics-overview.md) para visualização de dependência.<br/><br/> Você associa um espaço de trabalho novo ou existente do Log Analytics a um projeto do Azure Migrate. O espaço de trabalho para um projeto do Azure Migrate não pode ser modificado depois de adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura do projeto Azure Migrate.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou Europa Ocidental. Espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região na qual [o Mapa de Serviço é suportado](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate é marcado com a tecla Migration Project e o nome do projeto.
**Agentes necessários** | Em cada máquina que deseja analisar, instale os seguintes agentes:<br/><br/> O [agente de monitoramento da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [agente de dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se as máquinas locais não estão conectadas à internet, você precisa baixar e instalar o gateway Log Analytics neles.<br/><br/> Saiba mais sobre a instalação do [agente de dependência](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura do projeto Azure Migrate.<br/><br/> O Azure Migrate suporta espaços de trabalho residentes nas regiões leste dos EUA, sudeste da Ásia e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar em uma região na qual [o Mapa de Serviço é suportado](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> O espaço de trabalho para um projeto do Azure Migrate não pode ser modificado depois de adicionado.
**Custos** | A solução Mapa de Serviço não incorre em nenhuma cobrança pelos primeiros 180 dias (a partir do dia em que você associa o espaço de trabalho do Log Analytics ao projeto Azure Migrate)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> O uso de qualquer solução que não seja o Mapa de Serviço no espaço de trabalho log analytics associado incorrerá em [taxas padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa de Serviço não é gratuito e cada nó será cobrado de acordo com o nível pago do espaço de trabalho do Log Analytics/<br/><br/>Se você tiver projetos que você criou antes do Azure Migrate disponibilidade geral (GA- 28 de fevereiro de 2018), você pode ter incorrido em taxas adicionais do Mapa de Serviço. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes da GA ainda são cobrados.
**Gerenciamento** | Quando você registra agentes no espaço de trabalho, você usa o ID e a chave fornecidas pelo projeto Azure Migrate.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto Azure Migrate associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado pelo Azure Migrate, a menos que você exclua o projeto Azure Migrate. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se as máquinas não estão conectadas à internet, você precisa instalar o gateway Log Analytics neles.
**Azure Governamental** | A análise de dependência baseada em agentes não é suportada.

## <a name="next-steps"></a>Próximas etapas

[Prepare-se para avaliação de HiperV VM](tutorial-prepare-hyper-v.md)
