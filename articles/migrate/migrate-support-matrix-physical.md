---
title: Suporte para avaliação física do servidor no Azure Migrate
description: Saiba mais sobre o suporte para avaliação física do servidor com a avaliação do servidor Do Azure Migrate
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 4bf7af74be35a521cdaa02e9209a7d7c0b91184f
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389453"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matriz de suporte para avaliação física do servidor 

Este artigo resume os requisitos de pré-requisitos e suporte quando você avalia servidores físicos para migração para o Azure, usando a ferramenta [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Se você quiser migrar servidores físicos para o Azure, revise a [matriz de suporte à migração](migrate-support-matrix-physical-migration.md).


Para avaliar servidores físicos, crie um projeto do Azure Migrate e adicione a ferramenta Avaliação do Servidor ao projeto. Depois que a ferramenta é adicionada, você implanta o [aparelho Azure Migrate](migrate-appliance.md). O aparelho descobre continuamente máquinas no local e envia metadados da máquina e dados de desempenho para o Azure. Depois que a descoberta é concluída, você reúne máquinas descobertas em grupos e executa uma avaliação para um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 servidores físicos em um único [projeto Azure Migrate](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Além dos servidores físicos, um projeto pode incluir VMs VMware e VMs Hyper-V, até os limites de avaliação para cada um.
**Descoberta** | O aparelho Azure Migrate pode descobrir até 250 servidores físicos.
**Avaliação** | Você pode adicionar até 35.000 máquinas em um único grupo.<br/><br/> Você pode avaliar até 35.000 máquinas em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.

## <a name="physical-server-requirements"></a>Requisitos de servidor físico

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de servidor físico**       | O servidor físico pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | **Windows:** Você precisa de uma conta de usuário local ou domínio em todos os servidores Windows que você deseja descobrir. A conta de usuário deve ser adicionada a esses grupos: Usuários remotos de desktop, usuários do monitor de desempenho e usuários de registro de desempenho. <br/><br/> **Linux:** Você precisa de uma conta raiz nos servidores Linux que você deseja descobrir. |
| **Sistema Operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) que são suportados pelo Azure, exceto o Windows Server 2003 e o SUSE Linux.|


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

O Azure Migrate usa o [aparelho Azure Migrate](migrate-appliance.md) para descoberta e avaliação. O aparelho para servidores físicos pode funcionar em uma VM ou em uma máquina física. Você configura o aparelho usando um script PowerShell que você baixa no portal Azure.

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---physical) para servidores físicos.
- Saiba mais sobre [URLs](migrate-appliance.md#url-access) que o aparelho precisa acessar.

## <a name="port-access"></a>Acesso portuário

A tabela a seguir resume os requisitos da porta para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Aparelho** | Conexões de entrada na porta TCP 3389, para permitir conexões remotas de desktop ao aparelho.<br/><br/> Conexões de entrada na porta 44368, para acessar remotamente o aplicativo de gerenciamento do aparelho usando a URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Conexões de saída nas portas 443 (HTTPS), para enviar metadados de detecção e desempenho para o Azure Migrate.
**Servidores físicos** | **Windows:** Conexões de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS), para puxar metadados de configuração e desempenho de servidores Windows. <br/><br/> **Linux:**  Conexões de entrada na porta 22 (UDP), para puxar metadados de configuração e desempenho de servidores Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise de dependência baseados em agentes

[A análise de dependência](concepts-dependency-visualization.md) ajuda você a identificar dependências entre máquinas locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agentes. Atualmente, apenas a análise de dependência baseada em agentes é suportada para servidores físicos.

**Exigência** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto Azure Migrate em vigor, com a ferramenta Avaliação do servidor adicionada ao projeto.<br/><br/>  Você implanta visualização de dependência depois de configurar um aparelho Azure Migrate para descobrir suas máquinas no local<br/><br/> [Aprenda a](create-manage-projects.md) criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o aparelho Azure Migrate para avaliação de [servidores hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou físicos.
**Azure Governamental** | A visualização de dependência não está disponível no governo Azure.
**Análise de log** | O Azure Migrate usa a solução [Mapa de Serviço](../operations-management-suite/operations-management-suite-service-map.md) nos registros do Monitor do [Azure](../log-analytics/log-analytics-overview.md) para visualização de dependência.<br/><br/> Você associa um espaço de trabalho novo ou existente do Log Analytics a um projeto do Azure Migrate. O espaço de trabalho para um projeto do Azure Migrate não pode ser modificado depois de adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura do projeto Azure Migrate.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou Europa Ocidental. Espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região na qual [o Mapa de Serviço é suportado](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate é marcado com a tecla Migration Project e o nome do projeto.
**Agentes necessários** | Em cada máquina que deseja analisar, instale os seguintes agentes:<br/><br/> O [agente de monitoramento da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [agente de dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se as máquinas locais não estão conectadas à internet, você precisa baixar e instalar o gateway Log Analytics neles.<br/><br/> Saiba mais sobre a instalação do [agente de dependência](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura do projeto Azure Migrate.<br/><br/> O Azure Migrate suporta espaços de trabalho residentes nas regiões leste dos EUA, sudeste da Ásia e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar em uma região na qual [o Mapa de Serviço é suportado](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> O espaço de trabalho para um projeto do Azure Migrate não pode ser modificado depois de adicionado.
**Custos** | A solução Mapa de Serviço não incorre em nenhuma cobrança pelos primeiros 180 dias (a partir do dia em que você associa o espaço de trabalho do Log Analytics ao projeto Azure Migrate)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> O uso de qualquer solução que não seja o Mapa de Serviço no espaço de trabalho log analytics associado incorrerá em [taxas padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa de Serviço não é gratuito e cada nó será cobrado de acordo com o nível pago do espaço de trabalho do Log Analytics/<br/><br/>Se você tiver projetos que você criou antes do Azure Migrate disponibilidade geral (GA- 28 de fevereiro de 2018), você pode ter incorrido em taxas adicionais do Mapa de Serviço. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes da GA ainda são cobrados.
**Gerenciamento** | Quando você registra agentes no espaço de trabalho, você usa o ID e a chave fornecidas pelo projeto Azure Migrate.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto Azure Migrate associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado pelo Azure Migrate, a menos que você exclua o projeto Azure Migrate. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se as máquinas não estão conectadas à internet, você precisa instalar o gateway Log Analytics neles.

## <a name="next-steps"></a>Próximas etapas

[Prepare-se para avaliação física do servidor](tutorial-prepare-physical.md).
