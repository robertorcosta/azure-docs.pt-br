---
title: Suporte para avaliação de servidor físico nas migrações para Azure
description: Saiba mais sobre o suporte para avaliação de servidor físico com a avaliação de servidor de migrações para Azure
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 31fd676a339a6c82cec84e0f355ac875f68a653c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983662"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matriz de suporte para avaliação do servidor físico 

Este artigo resume os pré-requisitos e requisitos de suporte ao avaliar servidores físicos para migração para o Azure, usando a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Se você quiser migrar servidores físicos para o Azure, examine a [matriz de suporte de migração](migrate-support-matrix-physical-migration.md).


Para avaliar servidores físicos, você cria um projeto de migrações para Azure e adiciona a ferramenta de avaliação do servidor ao projeto. Depois que a ferramenta for adicionada, você implantará o [dispositivo migrações para Azure](migrate-appliance.md). O dispositivo descobre continuamente computadores locais e envia metadados de computador e dados de desempenho para o Azure. Após a conclusão da descoberta, você coleta computadores descobertos em grupos e executa uma avaliação de um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 servidores físicos em um único [projeto de migrações para Azure](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Além dos servidores físicos, um projeto pode incluir VMs VMware e VMs do Hyper-V, até os limites de avaliação de cada uma.
**Descoberta** | O dispositivo de migrações para Azure pode descobrir até 250 servidores físicos.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> Você pode avaliar até 35.000 computadores em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.

## <a name="physical-server-requirements"></a>Requisitos de servidor físico

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de servidor físico**       | O servidor físico pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | **Windows:** Você precisa ser um administrador de domínio ou administrador local em todos os servidores Windows que deseja descobrir. A conta de usuário deve ser adicionada a esses grupos: usuários de gerenciamento remoto, usuários de monitor de desempenho e usuários de log de desempenho. <br/><br/> **Linux:** Você precisa de uma conta raiz nos servidores Linux que deseja descobrir. |
| **Sistema operacional** | Todos os sistemas operacionais de servidor [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte do Azure, exceto para o Windows Server 2003 e o SuSE Linux.<br/><br/> Sistemas operacionais cliente Windows 10 e Windows 8. |


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

As migrações para Azure usam o [dispositivo de migrações para Azure](migrate-appliance.md) para descoberta e avaliação. O dispositivo para servidores físicos pode ser executado em uma VM ou em um computador físico. 

- Saiba mais sobre [os requisitos de dispositivo](migrate-appliance.md#appliance---physical) para servidores físicos.
- Saiba mais sobre URLs que o dispositivo precisa acessar em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls) .
- Você define o dispositivo usando um [script do PowerShell](how-to-set-up-appliance-physical.md) que você baixa do portal do Azure.
No Azure governamental, implante o dispositivo [usando esse script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Acesso à porta

A tabela a seguir resume os requisitos de porta para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Baseado** | Conexões de entrada na porta TCP 3389, para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368, para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Conexões de saída nas portas 443 (HTTPS), para enviar metadados de descoberta e desempenho para migrações para Azure.
**Servidores físicos** | **Windows:** Conexões de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS) para efetuar pull de metadados de configuração e desempenho de servidores Windows. <br/><br/> **Linux:**  Conexões de entrada na porta 22 (UDP) para efetuar pull de metadados de configuração e desempenho de servidores Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise de dependência baseada em agente

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre computadores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agente. Atualmente, somente a análise de dependência baseada em agente tem suporte para servidores físicos.

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto de migrações para Azure em vigor, com a ferramenta de avaliação do servidor adicionada ao projeto.<br/><br/>  Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir seus computadores locais<br/><br/> [Saiba como](create-manage-projects.md) criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o dispositivo de migrações para Azure para avaliação de servidores [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)ou físicos.
**Azure Governamental** | A visualização de dependência não está disponível no Azure governamental.
**Log Analytics** | As migrações para Azure usam a solução [mapa do serviço](../operations-management-suite/operations-management-suite-service-map.md) em [logs de Azure monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.<br/><br/> Você associa um espaço de trabalho Log Analytics novo ou existente a um projeto de migrações para Azure. O espaço de trabalho para um projeto de migrações para Azure não pode ser modificado após ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura que o projeto de migrações para Azure.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste asiático ou Europa Ocidental. Espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região na qual [mapa do serviço tem suporte](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> No Log Analytics, o espaço de trabalho associado à migração do Azure é marcado com a chave do projeto de migração e o nome do projeto.
**Agentes necessários** | Em cada computador que você deseja analisar, instale os seguintes agentes:<br/><br/> O [MMA (Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se os computadores locais não estiverem conectados à Internet, você precisará baixar e instalar Log Analytics gateway neles.<br/><br/> Saiba mais sobre como instalar o [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e o [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto de migrações para Azure.<br/><br/> As migrações para Azure dão suporte a espaços de trabalho que residem nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar em uma região na qual [mapa do serviço tem suporte](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> O espaço de trabalho para um projeto de migrações para Azure não pode ser modificado após ser adicionado.
**Com** | A solução de Mapa do Serviço não incorrerá em cobranças pelos primeiros 180 dias (a partir do dia em que você associa o espaço de trabalho Log Analytics com o projeto de migrações para Azure)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> Usar qualquer solução que não seja Mapa do Serviço no espaço de trabalho Log Analytics associado incorrerá em [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para log Analytics.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Depois de excluir o projeto, Mapa do Serviço uso não é gratuito e cada nó será cobrado de acordo com a camada paga de Log Analytics espaço de trabalho/<br/><br/>Se você tiver projetos criados antes de migrar a disponibilidade geral do Azure (GA-28 de fevereiro de 2018), você poderá ter incorrido Mapa do Serviço encargos adicionais. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes do GA ainda são passíveis de cobrança.
**Gerenciamento** | Ao registrar agentes no espaço de trabalho, você usa a ID e a chave fornecidas pelo projeto de migrações para Azure.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto de migrações do Azure associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado pela migração do Azure, a menos que você exclua o projeto de migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se os computadores não estiverem conectados à Internet, você precisará instalar o Log Analytics gateway neles.
**Azure Governamental** | Não há suporte para a análise de dependência baseada em agente.

## <a name="next-steps"></a>Próximas etapas

[Prepare-se para a avaliação do servidor físico](tutorial-prepare-physical.md).
