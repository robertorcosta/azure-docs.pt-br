---
title: Visão geral de Habilitar o Azure Monitor para VMs
description: Saiba como implantar e configurar Azure Monitor para VMs. Descubra os requisitos do sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: f5e774e9b7327d4b403f6a09187e97082a77aa78
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186790"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Visão geral de Habilitar o Azure Monitor para VMs

Este artigo fornece uma visão geral das opções disponíveis para permitir que o Azure Monitor para VMs monitore a integridade e o desempenho do seguinte:

- Máquinas virtuais do Azure 
- Conjuntos de dimensionamento de máquinas virtuais do Azure
- Máquinas virtuais híbridas conectadas com o arco do Azure
- Máquinas virtuais locais
- Máquinas virtuais hospedadas em outro ambiente de nuvem.  

Para configurar Azure Monitor para VMs:

* Habilite uma única VM do Azure, Azure VMSS ou máquina de Arc do Azure selecionando **insights** diretamente em seu menu na portal do Azure.
* Habilite várias VMs do Azure, VMSS do Azure ou máquinas de Arc do Azure usando Azure Policy. Esse método garante que, em VMs e conjuntos de dimensionamento novos e existentes, as dependências necessárias sejam instaladas e configuradas corretamente. VMs não compatíveis e conjuntos de dimensionamento são relatados, para que você possa decidir se deseja habilitá-los e corrigi-los.
* Habilite várias VMs do Azure, VMs de arco do Azure, Azure VMSS ou máquinas de Arc do Azure em uma assinatura ou grupo de recursos especificado usando o PowerShell.
* Habilite Azure Monitor para VMs para monitorar VMs ou computadores físicos hospedados em sua rede corporativa ou em outro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você compreende as informações descritas nas seções a seguir. 

>[!NOTE]
>As informações a seguir descritas nesta seção também são aplicáveis à [solução de mapa do serviço](service-map.md).  

### <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics

O Azure Monitor para VMs dá suporte a um espaço de trabalho do Log Analytics nas seguintes regiões:

- África
  - Norte da África do Sul
- Pacífico Asiático
  - Leste da Ásia
  - Sudeste Asiático
- Austrália
  - Leste da Austrália
  - Sudeste da Austrália
- Azure Government
  - US Gov AZ
  - VA US Gov
- Canada
  - Canadá Central
- Europa
  - Norte da Europa
  - Europa Ocidental
- Índia
  - Índia Central
- Japão
  - Leste do Japão
- Reino Unido
  - Sul do Reino Unido
- Estados Unidos
  - Centro dos EUA
  - Leste dos EUA
  - Leste dos EUA 2
  - Centro-Norte dos EUA
  - Centro-Sul dos Estados Unidos
  - Centro-Oeste dos EUA
  - Oeste dos EUA
  - Oeste dos EUA 2


>[!NOTE]
>Você pode monitorar as VMs do Azure em qualquer região. As VMs em si não são limitadas às regiões com suporte no espaço de trabalho Log Analytics.
>

Se você não tiver um espaço de trabalho Log Analytics, poderá criar um usando um dos recursos:
* [CLI do Azure](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure portal](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

- Máquina virtual do Azure
- Conjunto de dimensionamento de máquinas virtuais do Azure
- Máquina virtual híbrida conectada ao Azure Arc

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

O Azure Monitor para VMs dá suporte a qualquer sistema operacional que ofereça suporte ao agente de Log Analytics e ao agente de dependência. Consulte [visão geral de agentes de Azure monitor ](../platform/agents-overview.md#supported-operating-systems) para obter uma lista completa.

Consulte a seguinte lista de considerações sobre o suporte do Linux do agente de dependência que dá suporte a Azure Monitor para VMs:

- Somente as versões de kernel padrão e Linux SMP têm suporte.
- Não há suporte para nenhuma distribuição do Linux em versões de kernel não padrão, como PAE (Extensão do Endereço Físico) e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão *2.6.16.21-0.8-xen*.
- Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.
- Para Debian distribuições que não seja a versão 9,4, o recurso de mapa não tem suporte e o recurso de desempenho está disponível apenas no menu Azure Monitor. Ele não está disponível diretamente no painel esquerdo da VM do Azure.
- Há suporte para o kernel CentOSPlus.
- O kernel do Linux deve ser corrigido para a vulnerabilidade Spectre. Consulte seu fornecedor de distribuição do Linux para obter mais detalhes.



## <a name="supported-azure-arc-machines"></a>Máquinas do Arc do Azure com suporte
Azure Monitor para VMs está disponível para servidores habilitados para Arc do Azure em regiões em que o serviço de extensão Arc está disponível. Você deve estar executando a versão 0,9 ou superior do agente Arc.

| Fonte conectada | Com suporte | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Junto com o [agente do log Analytics para Windows](../platform/log-analytics-agent.md), os agentes do Windows precisam do agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](../platform/agents-overview.md#supported-operating-systems). |
| Agentes do Linux | Sim | Junto com o [agente de log Analytics para Linux](../platform/log-analytics-agent.md), os agentes do Linux precisam do agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](#supported-operating-systems). |
| Grupo de gerenciamento do System Center Operations Manager | Não | |

## <a name="agents"></a>Agentes
Azure Monitor para VMs requer que os dois agentes a seguir sejam instalados em cada máquina virtual ou conjunto de dimensionamento de máquinas virtuais a ser monitorado. Instalar esses agentes e conectá-los ao espaço de trabalho é o único requisito para carregar o recurso.

- [Agente de log Analytics](../platform/log-analytics-agent.md). Coleta eventos e dados de desempenho da máquina virtual ou do conjunto de dimensionamento de máquinas virtuais e os entrega ao espaço de trabalho do Log Analytics. Os métodos de implantação para o agente de Log Analytics nos recursos do Azure usam a extensão de VM para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md).
- Agente de dependência. Coleta dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos, que são usados pelo [recurso de mapa no Azure monitor para VMs](vminsights-maps.md). O agente de dependência depende do agente de Log Analytics para entregar seus dados para Azure Monitor. Os métodos de implantação para o Dependency Agent nos recursos do Azure usam a extensão de VM para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> O agente de Log Analytics é o mesmo agente usado pelo System Center Operations Manager. Azure Monitor para VMs pode monitorar agentes que também são monitorados pelo Operations Manager se eles estão diretamente conectados e você instala o agente de dependência neles. Os agentes conectados a Azure Monitor por meio de uma [conexão do grupo de gerenciamento](../tform/../platform/om-agents.md) não podem ser monitorados pelo Azure monitor para VMs.

A seguir estão vários métodos para implantar esses agentes. 

| Método | Descrição |
|:---|:---|
| [Azure portal](./vminsights-enable-portal.md) | Instale os dois agentes em uma única máquina virtual, conjunto de dimensionamento de máquinas virtuais ou máquinas virtuais híbridas conectadas com o arco do Azure. |
| [Modelos do Gerenciador de Recursos](vminsights-enable-resource-manager.md) | Instale ambos os agentes usando qualquer um dos métodos com suporte para implantar um modelo do Resource Manager, incluindo a CLI e o PowerShell. |
| [Azure Policy](./vminsights-enable-policy.md) | Atribua Azure Policy Initiative para instalar automaticamente os agentes quando uma máquina virtual ou um conjunto de dimensionamento de máquinas virtuais for criado. |
| [Instalação manual](./vminsights-enable-hybrid.md) | Instale os agentes no sistema operacional convidado em computadores hospedados fora do Azure, incluindo em seu datacenter ou em outros ambientes de nuvem. |




## <a name="management-packs"></a>Pacotes de gerenciamento
Quando um espaço de trabalho Log Analytics é configurado para Azure Monitor para VMs, dois pacotes de gerenciamento são encaminhados para todos os computadores Windows conectados a esse espaço de trabalho. Os pacotes de gerenciamento são nomeados *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* e *Microsoft. IntelligencePacks. VMInsights* e são gravados em *%ProgramFiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management packs \* . 

A fonte de dados usada pelo pacote de gerenciamento *ApplicationDependencyMonitor* é **% Program Files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. A fonte de dados usada pelo pacote de gerenciamento *VMInsights* é *% Program Files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico

A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Azure Monitor. A Microsoft usa esses dados para melhorar a qualidade, a segurança e a integridade do serviço. 

Para fornecer recursos de solução de problemas precisos e eficientes, o recurso de mapa inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operacional e a versão, o endereço IP, o nome DNS e o nome da estação de trabalho. A Microsoft não coleta nomes, endereços nem outras informações de contato.

Para obter mais informações sobre a coleta e uso de dados, consulte a [Política de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber como usar o recurso de monitoramento de desempenho, consulte [exibir Azure monitor para VMs desempenho](vminsights-performance.md). Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).
