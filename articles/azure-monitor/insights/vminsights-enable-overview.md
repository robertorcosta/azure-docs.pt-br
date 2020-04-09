---
title: Habilite o Monitor Do Azure para visão geral das VMs
description: Saiba como implantar e configurar o Monitor Azure para VMs. Descubra os requisitos do sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982303"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Habilite o Monitor Do Azure para visão geral das VMs

Este artigo fornece uma visão geral das opções disponíveis para habilitar o Monitor Azure para VMs em suas máquinas virtuais para monitorar a saúde e o desempenho. Descubra dependências de aplicativos que são executados em máquinas virtuais (VMs) e conjuntos de escala de máquinas virtuais, VMs ou VMs hospedados em outro ambiente em nuvem.  

Para configurar o Monitor Azure para VMs:

* Habilite uma única escala de VM ou máquina virtual do Azure, selecionando **Insights** diretamente do conjunto de escalas de VM ou máquina virtual.
* Habilite duas ou mais VMs azure e conjuntos de escala de máquinas virtuais usando a Diretiva Azure. Este método garante que, em VMs e conjuntos de escala existentes e novos, as dependências necessárias sejam instaladas e configuradas adequadamente. VMs e conjuntos de escala não compatíveis são relatados, para que você possa decidir se os habilita e remedia-los.
* Habilite duas ou mais VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais em uma assinatura ou um grupo de recursos especificado usando o PowerShell.
* Habilite o Monitor Azure para VMs para monitorar VMs ou computadores físicos hospedados em sua rede corporativa ou outro ambiente em nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você compreende as informações descritas nas seções a seguir. 

>[!NOTE]
>As seguintes informações descritas nesta seção também são aplicáveis à [solução Mapa de Serviço](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

O Azure Monitor for VMs suporta um espaço de trabalho do Log Analytics nas seguintes regiões:

- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Centro-Sul dos Estados Unidos
- Leste dos EUA
- Leste dos EUA 2
- Centro dos EUA
- Centro-Norte dos EUA
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
>Você pode monitorar VMs azure em qualquer região. As VMs em si não se limitam às regiões suportadas pelo espaço de trabalho Log Analytics.
>

Se você não tiver um espaço de trabalho do Log Analytics, você pode criar um usando um dos recursos:
* [CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Portal Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Você também pode criar um espaço de trabalho enquanto estiver habilitando o monitoramento de uma única VM Azure ou escala de máquina virtual definida no portal Azure.

Para configurar um cenário em escala que use modelos de Azure Policy, Azure PowerShell ou Azure Resource Manager, em seu espaço de trabalho do Log Analytics:

* Instale as soluções *ServiceMap* e *InfrastructureInsights.* Você pode concluir esta instalação usando um modelo do Azure Resource Manager fornecido. Ou na guia **'Iniciar',** no portal Azure, **selecione Configurar espaço de trabalho**.
* Configure o espaço de trabalho do Log Analytics para coletar contadores de desempenho.

Para configurar seu espaço de trabalho para o cenário em escala, use um dos seguintes métodos:

* Use [o Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na página Azure Monitor for VMs [**Policy Coverage,**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) selecione **Configurar espaço de trabalho**. 

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

A tabela a seguir lista os sistemas operacionais Windows e Linux que o Azure Monitor para VMs suporta. Mais tarde, nesta seção, você encontrará uma lista completa que detalha a versão principal e menor do Sistema Operacional Linux e as versões suportadas do kernel.

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
|Ubuntu 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SLES (SUSE Linux Enterprise Server) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> O recurso Desempenho do Azure Monitor para VMs está disponível somente no Azure Monitor. Não está disponível diretamente do painel esquerdo da VM Azure.

>[!NOTE]
>No sistema operacional Linux:
> - Somente as versões de kernel padrão e Linux SMP têm suporte.
> - Não há suporte para nenhuma distribuição do Linux em versões de kernel não padrão, como PAE (Extensão do Endereço Físico) e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão *2.6.16.21-0.8-xen*.
> - Os núcleos personalizados, incluindo recompilações de kernels padrão, não são suportados.
> - O kernel CentOSPlus é suportado.
> - O kernel Linux deve ser corrigido para a vulnerabilidade Spectre. Consulte seu fornecedor de distribuição Linux para obter mais detalhes.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão do kernel |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
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
| 18.04 | 5.0 (inclui kernel azure-tuned)<br>4.18*<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão do kernel |
|:--|:--|
|12 SP4 | 4.12.* (inclui kernel azure-tuned) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Versão do SO | Versão do kernel |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

O recurso Mapa no Monitor Do Azure para VMs obtém seus dados do agente Microsoft Dependency. O Dependency Agent depende do agente do Log Analytics para suas conexões com o Log Analytics. Assim, seu sistema deve ter o agente Log Analytics instalado e configurado com o agente Dependency.

Se você habilita o Monitor Azure para VMs para uma única VM azure ou usa o método de implantação em escala, use a extensão do agente azure VM Dependency para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) ou [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) para instalar o agente como parte da experiência.

>[!NOTE]
>As seguintes informações descritas nesta seção também são aplicáveis à [solução Mapa de Serviço](service-map.md).  

Em um ambiente híbrido, você pode baixar e instalar o agente Dependency manualmente ou usando um método automatizado.

A tabela a seguir descreve as fontes conectadas para as quais o recurso Mapa dá suporte em um ambiente híbrido.

| Fonte conectada | Com suporte | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Juntamente com o [agente Log Analytics para Windows,](../../azure-monitor/platform/log-analytics-agent.md)os agentes do Windows precisam do agente dependency. Para obter mais informações, consulte [sistemas operacionais suportados.](#supported-operating-systems) |
| Agentes do Linux | Sim | Juntamente com o [agente Log Analytics para Linux,](../../azure-monitor/platform/log-analytics-agent.md)os agentes Linux precisam do agente dependency. Para obter mais informações, consulte [sistemas operacionais suportados.](#supported-operating-systems) |
| Grupo de gerenciamento do System Center Operations Manager | Não | |

Você pode baixar o agente dependency desses locais:

| Arquivo | Sistema operacional | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE995F5A4633d8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BD293968F02A9EF821F9639406A1BDF1F6792DB9E00D54A7F0B |

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Para habilitar e acessar os recursos do Azure Monitor para VMs, você deve ter a função de contribuinte do *Log Analytics.* Para visualizar dados de desempenho, saúde e mapa, você deve ter a função *de leitor de monitoramento* para a VM do Azure. O espaço de trabalho do Log Analytics precisa ser configurado para o Azure Monitor para VMs.

Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms"></a>Como ativar o Monitor Azure para VMs

Habilite o Monitor Azure para VMs usando um dos métodos descritos nesta tabela:

| Estado da implantação | Método | Descrição |
|------------------|--------|-------------|
| Conjunto de escala de máquinas virtuais ou Azure único | [Habilitar a partir da VM](vminsights-enable-single-vm.md) | Você pode habilitar uma única VM Azure selecionando **Insights** diretamente do conjunto de escalas de VM ou máquina virtual. |
| Vários VMs azure ou conjuntos de escala de máquinas virtuais | [Habilite através da Política Do Azure](vminsights-enable-at-scale-policy.md) | Você pode habilitar várias VMs do Azure usando a diretiva do Azure e as definições de diretiva disponíveis. |
| Vários VMs azure ou conjuntos de escala de máquinas virtuais | [Habilite através de modelos azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Você pode habilitar vários Conjuntos de VMs do Azure ou de máquinas virtuais em um grupo de assinatura ou recurso especificado usando modelos do Azure PowerShell ou do Azure Resource Manager. |
| Nuvem híbrida | [Habilitar para o ambiente híbrido](vminsights-enable-hybrid-cloud.md) | Você pode implantar em VMs ou computadores físicos hospedados em seu data center ou outros ambientes em nuvem. |

## <a name="management-packs"></a>Pacotes de gerenciamento

Quando o Monitor Do Azure para VMs é ativado e configurado com um espaço de trabalho do Log Analytics, um pacote de gerenciamento é encaminhado para todos os computadores Windows que reportam para esse espaço de trabalho. Se você [integrou o grupo de gerenciamento do System Center Operations Manager](../../azure-monitor/platform/om-agents.md) com o espaço de trabalho do Log Analytics, o pacote de gerenciamento do Mapa de Serviços será implantado do grupo de gerenciamento para os computadores Windows reportando ao grupo de gerenciamento.  

O pacote de gerenciamento é chamado *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. Está escrito `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` para pasta. A fonte de dados que `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`o pacote de gerenciamento usa é .

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico

A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Azure Monitor. A Microsoft usa esses dados para melhorar a qualidade, a segurança e a integridade do serviço. 

Para fornecer recursos precisos e eficientes de solução de problemas, o recurso Mapa inclui dados sobre a configuração do seu software. Os dados fornecem informações como o sistema operacional e a versão, endereço IP, nome do DNS e nome da estação de trabalho. A Microsoft não coleta nomes, endereços nem outras informações de contato.

Para obter mais informações sobre a coleta e uso de dados, consulte a [Política de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Agora que você habilitou o monitoramento de sua VM, as informações de monitoramento estão disponíveis para análise no Monitor Azure para VMs.

## <a name="next-steps"></a>Próximas etapas

Para saber como usar o recurso de monitoramento de desempenho, consulte [Exibir monitor do Azure para desempenho de VMs](vminsights-performance.md). Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).
