---
title: Gerenciamento do ciclo de vida de VMs da solução Azure VMware
description: Saiba como gerenciar todos os aspectos do ciclo de vida de suas VMs de solução do Azure VMware com Microsoft Azure ferramentas nativas.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 452fa6bf4610c9d0dcb46587242e8d6df9cebd0d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950356"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Gerenciamento do ciclo de vida de VMs da solução Azure VMware

Microsoft Azure ferramentas nativas permitem que você monitore e gerencie suas VMs (máquinas virtuais) no ambiente do Azure. Ainda assim, eles também permitem que você monitore e gerencie suas VMs na solução VMware do Azure e suas VMs locais. Nesta visão geral, veremos a arquitetura de monitoramento integrado que o Azure oferece e como você pode usar suas ferramentas nativas para gerenciar suas VMs de solução do Azure VMware em todo o ciclo de vida.

## <a name="benefits"></a>Vantagens

- Os serviços nativos do Azure podem ser usados para gerenciar suas VMs em um ambiente híbrido (Azure, solução do Azure VMware e local).
- Monitoramento e visibilidade integrados de seu Azure, solução Azure VMware e VMs locais.
- Com o Azure Gerenciamento de Atualizações na automação do Azure, você pode gerenciar atualizações do sistema operacional para suas máquinas Windows e Linux. 
- A central de segurança do Azure fornece proteção avançada contra ameaças, incluindo:
    - Monitoramento de integridade do arquivo
    - Alertas de segurança sem arquivo
    - Avaliação de patch do sistema operacional
    - Avaliação de configurações incorretas de segurança
    - Avaliação da proteção de ponto de extremidade 
- Implante facilmente o Microsoft Monitoring Agent (MMA) usando o Azure ARC para novas VMs. 
- Seu espaço de trabalho Log Analytics no Azure Monitor habilita a coleta de logs e a coleta de contadores de desempenho usando MMA ou extensões. Colete dados e logs para um único ponto e apresente esses dados a diferentes serviços nativos do Azure. 
- Os benefícios adicionais do Azure Monitor incluem: 
    - Monitoramento contínuo 
    - Melhor visibilidade da infraestrutura 
    - Notificações instantâneas 
    - Resolução automática 
    - Eficiência de custo 

## <a name="integrated-azure-monitoring-architecture"></a>Arquitetura de monitoramento do Azure integrada

O diagrama a seguir mostra a arquitetura de monitoramento integrado para VMs de solução do Azure VMware.

![Arquitetura de monitoramento do Azure integrada](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrando e implantando os serviços nativos do Azure

O **Arc do Azure** estende o gerenciamento do Azure para qualquer infraestrutura, incluindo a solução Azure VMware, local ou outras plataformas de nuvem. O ARC do Azure pode ser implantado instalando um cluster AKS (serviço kubernetes do Azure) no ambiente da solução VMware do Azure. Para obter mais informações, consulte [conectar um cluster kubernetes habilitado para Arc do Azure](../azure-arc/kubernetes/connect-cluster.md).

As VMs da solução Azure VMware podem ser monitoradas por meio do MMA (também conhecido como agente de Log Analytics ou agente do OMS para Linux). O MMA pode ser instalado automaticamente quando as VMs são provisionadas por meio de fluxos de trabalho de ciclo de vida da VM ARC. O MMA também pode ser instalado ao implantar VMs de um modelo no vCenter; novamente, com VMs provisionadas por meio de fluxos de trabalho ARC. Todas as VMs de solução do Azure VMware provisionadas podem ter o MMA instalado e enviar logs para o espaço de trabalho do Azure Log Analytics. Para obter mais informações, consulte [visão geral do agente de log Analytics](../azure-monitor/platform/log-analytics-agent.md).

**Log Analytics espaço de trabalho** habilita a coleta de logs e a coleta de contadores de desempenho usando MMA ou extensões. Para criar seu espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md).
- Para adicionar VMs do Windows ao seu espaço de trabalho do log Analytics, consulte [instalar log Analytics agente em computadores Windows](../azure-monitor/platform/agent-windows.md).
- Para adicionar VMs do Linux ao seu espaço de trabalho do log Analytics, consulte [instalar log Analytics agente em computadores Linux](../azure-monitor/platform/agent-linux.md).

O **azure gerenciamento de atualizações** na automação do Azure gerencia atualizações do sistema operacional para seus computadores Windows e Linux em um ambiente híbrido. Ele monitora a conformidade de patches e encaminha alertas de desvio de aplicação de patch para Azure Monitor para correção. O Azure Gerenciamento de Atualizações deve se conectar ao seu espaço de trabalho do Log Analytics para usar dados armazenados para avaliar o status das atualizações em suas VMs.
- Para adicionar Log Analytics ao Gerenciamento de Atualizações do Azure, primeiro você precisa [criar uma conta de automação do Azure](../automation/automation-create-standalone-account.md).
- Para vincular seu espaço de trabalho do Log Analytics à sua conta de automação, confira [log Analytics espaço de trabalho e conta de automação](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).
- Para habilitar o Azure Gerenciamento de Atualizações para suas VMs, consulte [habilitar gerenciamento de atualizações de uma conta de automação](../automation/update-management/update-mgmt-enable-automation-account.md).
- Depois de adicionar VMs ao Gerenciamento de Atualizações do Azure, você pode [implantar atualizações em VMs e examinar os resultados](../automation/update-management/update-mgmt-deploy-updates.md). 

A **central de segurança do Azure** fornece proteção avançada contra ameaças em suas cargas de trabalho híbridas na nuvem e localmente. Ele avaliará a vulnerabilidade das VMs de solução do Azure VMware e emitirá alertas conforme necessário. Esses alertas de segurança podem ser encaminhados para Azure Monitor para resolução.
- A central de segurança do Azure não requer implantação. Para obter mais informações, consulte uma lista de [recursos com suporte para máquinas virtuais](../security-center/security-center-services.md).
- Para adicionar VMs de solução do Azure VMware e VMs não Azure à central de segurança do Azure, confira integrar [computadores com Windows à central de segurança do Azure](../security-center/quickstart-onboard-machines.md) e integrar [computadores Linux à central de segurança do Azure](../security-center/quickstart-onboard-machines.md).
- Depois de adicionar as VMs, a central de segurança do Azure analisa o estado de segurança dos recursos para identificar possíveis vulnerabilidades. Ele também fornece recomendações na guia Visão geral. Para obter mais informações, consulte [recomendações de segurança na central de segurança do Azure](../security-center/security-center-recommendations.md).
- Você pode definir políticas de segurança na central de segurança do Azure. Para obter informações sobre como configurar suas políticas de segurança, consulte [trabalhando com políticas de segurança](../security-center/tutorial-security-policy.md).

**Azure monitor** é uma solução abrangente para coletar, analisar e agir na telemetria de seus ambientes locais e na nuvem. Ele não requer nenhuma implantação.
- Azure Monitor permite que você colete dados de fontes diferentes para monitorar e analisar. Para obter mais informações, consulte [fontes de dados de monitoramento para Azure monitor](../azure-monitor/platform/data-sources.md).
- Você também pode coletar diferentes tipos de dados para análise, visualização e alertas. Para obter mais informações, consulte [Azure monitor Data Platform](../azure-monitor/platform/data-platform.md).
- Para configurar Azure Monitor com seu espaço de trabalho do Log Analytics, consulte [configurar log Analytics espaço de trabalho para Azure monitor para VMs](../azure-monitor/insights/vminsights-configure-workspace.md).
- Você pode criar regras de alerta para identificar problemas em seu ambiente, como alto uso de recursos, patches ausentes, pouco espaço em disco e pulsação de suas VMs. Você também pode definir uma resposta automatizada para eventos detectados enviando um alerta para as ferramentas de gerenciamento de serviços de ti (ITSM). A notificação de detecção de alerta também pode ser enviada por email. Para criar essas regras, consulte:
    - [Crie, exiba e gerencie alertas de métrica usando Azure monitor](../azure-monitor/platform/alerts-metric.md).
    - [Crie, exiba e gerencie alertas de log usando Azure monitor](../azure-monitor/platform/alerts-log.md).
    - [Regras de ação](../azure-monitor/platform/alerts-action-rules.md) para definir ações e notificações automatizadas.
    - [Conecte o Azure a ferramentas de ITSM usando o conector de gerenciamento de serviços de ti](../azure-monitor/platform/itsmc-overview.md).

O **PaaS (plataforma como serviço) do Azure** é um ambiente de desenvolvimento e implantação na nuvem, com recursos que permitem que você forneça aplicativos baseados em nuvem. Por exemplo, você pode integrar o banco de dados SQL do Azure com suas VMs de solução do Azure VMware. Os alertas do SQL podem então ser correlacionados com os alertas da VM da solução Azure VMware. Por exemplo, digamos que o trecho do banco de dados SQL do seu aplicativo esteja dentro da PAAS do Azure e a camada de aplicativo Web do mesmo aplicativo seja hospedada em suas VMs da solução Azure VMware. Os alertas de banco de dados podem ser correlacionados com alertas de aplicativos Web. A solução de problemas é simplificada com uma única visibilidade integrada do Azure, da solução Azure VMware e de VMs locais.