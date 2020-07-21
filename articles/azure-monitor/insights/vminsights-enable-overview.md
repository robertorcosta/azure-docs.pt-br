---
title: Habilitar Azure Monitor para VMs visão geral
description: Saiba como implantar e configurar Azure Monitor para VMs. Descubra os requisitos do sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 072f8fd44fa45648afd15cb40cba26bb427c7b56
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539611"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Habilitar Azure Monitor para VMs visão geral

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

### <a name="log-analytics"></a>Log Analytics

O Azure Monitor para VMs dá suporte a um espaço de trabalho do Log Analytics nas seguintes regiões:

- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- South Central US
- East US
- Leste dos EUA 2
- Centro dos EUA
- Centro-Norte dos EUA
- US Gov AZ
- VA US Gov
- Canadá Central
- Sul do Reino Unido
- Norte da Europa
- Europa Ocidental
- Leste da Ásia
- Sudeste Asiático
- Índia Central
- Leste do Japão
- Leste da Austrália
- Sudeste da Austrália

>[!NOTE]
>Você pode monitorar as VMs do Azure em qualquer região. As VMs em si não são limitadas às regiões com suporte no espaço de trabalho Log Analytics.
>

Se você não tiver um espaço de trabalho Log Analytics, poderá criar um usando um dos recursos:
* [CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Você também pode criar um espaço de trabalho enquanto estiver habilitando o monitoramento para uma única VM do Azure ou um conjunto de dimensionamento de máquinas virtuais no portal do Azure.

Para configurar um cenário em escala que usa Azure Policy, Azure PowerShell ou modelos de Azure Resource Manager, você deve instalar a solução *VMInsights* . Você pode fazer isso com um dos seguintes métodos:

* Use [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na página [**cobertura da política**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) de Azure monitor para VMs, selecione **Configurar espaço de trabalho**. 

### <a name="azure-arc-machines"></a>Computadores do Arc do Azure
Azure Monitor para VMs está disponível para servidores habilitados para Arc do Azure em regiões em que o serviço de extensão Arc está disponível. Os usuários devem estar executando a versão 0,9 ou superior do agente ARC para habilitar Azure Monitor para VMs em seus servidores habilitados para Arc.

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

A tabela a seguir lista os sistemas operacionais Windows e Linux aos quais Azure Monitor para VMs dá suporte. Mais adiante nesta seção, você encontrará uma lista completa que detalha a versão principal e secundária do sistema operacional Linux e as versões de kernel com suporte.

|Versão do SO |Desempenho |Mapas |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18, 4, 16, 4 | X | X |
|CentOS Linux 7, 6 | X | X |
|SLES (SUSE Linux Enterprise Server) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> O recurso Desempenho do Azure Monitor para VMs está disponível somente no Azure Monitor. Ele não está disponível diretamente no painel esquerdo da VM do Azure.

>[!NOTE]
>No sistema operacional Linux:
> - Somente as versões de kernel padrão e Linux SMP têm suporte.
> - Não há suporte para nenhuma distribuição do Linux em versões de kernel não padrão, como PAE (Extensão do Endereço Físico) e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão *2.6.16.21-0.8-xen*.
> - Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.
> - Há suporte para o kernel CentOSPlus.
> - O kernel do Linux deve ser corrigido para a vulnerabilidade Spectre. Consulte seu fornecedor de distribuição do Linux para obter mais detalhes.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão do kernel |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão do kernel |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Versão do SO | Versão do kernel |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão do kernel |
|:--|:--|
| 18.04 | 5.3.0-1020<br>5,0 (inclui kernel ajustado pelo Azure)<br>4,18* <br> 4,15* |
| 16.04.3 | 4,15. * |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão do kernel |
|:--|:--|
|12 SP4 | 4,12. * (inclui kernel ajustado para o Azure) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Versão do SO | Versão do kernel |
|:--|:--|
| 9 | 4,9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

O recurso de mapa no Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. O Dependency Agent depende do agente do Log Analytics para suas conexões com o Log Analytics. Portanto, seu sistema deve ter o agente de Log Analytics instalado e configurado com o Dependency Agent.

Se você habilitar Azure Monitor para VMs para uma única VM do Azure ou usar o método de implantação em escala, use a extensão do agente de dependência de VM do Azure para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) ou [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) para instalar o agente como parte da experiência.

>[!NOTE]
>As informações a seguir descritas nesta seção também são aplicáveis à [solução de mapa do serviço](service-map.md).  

Em um ambiente híbrido, você pode baixar e instalar o agente de dependência manualmente ou usando um método automatizado.

A tabela a seguir descreve as fontes conectadas para as quais o recurso Mapa dá suporte em um ambiente híbrido.

| Fonte conectada | Com suporte | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Junto com o [agente do log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Windows precisam do agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](#supported-operating-systems). |
| Agentes do Linux | Sim | Junto com o [agente de log Analytics para Linux](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Linux precisam do agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](#supported-operating-systems). |
| Grupo de gerenciamento do System Center Operations Manager | Não | |

Você pode baixar o Dependency Agent nestes locais:

| Arquivo | Sistema operacional | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.4.10090 | B4E1FF9C1E5CD254AA709AEF9723A81F04EC0763C327567C582CE99C0C5A0BAE  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.4.10090 | A56E310D297CE3B343AE8F4A6F72980F1C3173862D6169F1C713C2CA09660A9F |

## <a name="role-based-access-control"></a>Controle de acesso baseado em funções

Para habilitar e acessar os recursos no Azure Monitor para VMs, você deve ter a função de *colaborador de log Analytics* . Para exibir o desempenho, a integridade e os dados do mapa, você deve ter a função *leitor de monitoramento* para a VM do Azure. O espaço de trabalho do Log Analytics precisa ser configurado para o Azure Monitor para VMs.

Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms"></a>Como habilitar Azure Monitor para VMs

Habilite Azure Monitor para VMs usando um dos métodos descritos nesta tabela:

| Estado da implantação | Método | Descrição |
|------------------|--------|-------------|
| VM do Azure única, Azure VMSS ou máquina de Arc do Azure | [Habilitar no portal](vminsights-enable-single-vm.md) | Selecione **insights** diretamente no menu na portal do Azure. |
| Várias VMs do Azure, Azure VMSS ou máquina de Arc do Azure | [Habilitar por meio de Azure Policy](vminsights-enable-at-scale-policy.md) | Use Azure Policy para habilitar automaticamente quando uma VM ou VMSS for criada. |
| | [Habilitar por meio de modelos Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Use modelos Azure PowerShell ou Azure Resource Manager para habilitar várias VMs do Azure, VM de arco do Azure ou VMSS do Azure em uma assinatura ou grupo de recursos especificado pelo. |
| Nuvem híbrida | [Habilitar para o ambiente híbrido](vminsights-enable-hybrid-cloud.md) | Implante em VMs ou computadores físicos hospedados em seu datacenter ou em outros ambientes de nuvem. |

## <a name="management-packs"></a>Pacotes de gerenciamento

Quando Azure Monitor para VMs é habilitado e configurado com um espaço de trabalho do Log Analytics, um pacote de gerenciamento é encaminhado para todos os computadores Windows que se reportam a esse espaço de trabalho. Se você [integrou seu grupo de gerenciamento de System Center Operations Manager](../../azure-monitor/platform/om-agents.md) com o espaço de trabalho log Analytics, o pacote de gerenciamento do mapa do serviço é implantado do grupo de gerenciamento para os computadores Windows que se reportam ao grupo de gerenciamento.  

O pacote de gerenciamento é denominado *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. Sua gravação na `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` pasta. A fonte de dados que o pacote de gerenciamento usa é `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll` .

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico

A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Azure Monitor. A Microsoft usa esses dados para melhorar a qualidade, a segurança e a integridade do serviço. 

Para fornecer recursos de solução de problemas precisos e eficientes, o recurso de mapa inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operacional e a versão, o endereço IP, o nome DNS e o nome da estação de trabalho. A Microsoft não coleta nomes, endereços nem outras informações de contato.

Para obter mais informações sobre a coleta e uso de dados, consulte a [Política de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Agora que você habilitou o monitoramento para sua VM, as informações de monitoramento estão disponíveis para análise no Azure Monitor para VMs.

## <a name="next-steps"></a>Próximas etapas

Para saber como usar o recurso de monitoramento de desempenho, consulte [exibir Azure monitor para VMs desempenho](vminsights-performance.md). Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).
