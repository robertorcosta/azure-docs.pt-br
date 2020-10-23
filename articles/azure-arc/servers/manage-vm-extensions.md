---
title: Gerenciamento de extensão de VM com servidores habilitados para Arc do Azure
description: Os servidores habilitados para Arc do Azure podem gerenciar a implantação de extensões de máquina virtual que fornecem tarefas de automação e configuração pós-implantação com VMs não Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: e9865761fd3e5897ee3f01cd3d6ca620d5ea2f4b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460879"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gerenciamento de extensão de máquina virtual com servidores habilitados para Arc do Azure

As extensões de VM (máquina virtual) são pequenos aplicativos que fornecem tarefas de configuração e automação de pós-implantação em VMs do Azure. Por exemplo, se uma máquina virtual exigir instalação de software, proteção antivírus ou executar um script dentro dela, uma extensão de VM poderá ser usada.

Os servidores habilitados para Arc do Azure permitem implantar extensões de VM do Azure em VMs não Azure Windows e Linux, simplificando o gerenciamento de seu computador híbrido local, Edge e outros ambientes de nuvem por meio de seu ciclo de vida. As extensões de VM podem ser gerenciadas usando os seguintes métodos em seus computadores híbridos ou servidores gerenciados por servidores habilitados para Arc:

- O [Portal do Azure](manage-vm-extensions-portal.md)
- A [CLI do Azure](manage-vm-extensions-cli.md)
- [PowerShell do Azure](manage-vm-extensions-powershell.md)
- Modelos do Azure [Resource Manager](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Principais benefícios

O suporte à extensão de VM dos servidores habilitados para Arc do Azure oferece os seguintes benefícios principais:

- Use a [configuração de estado da automação do Azure](../../automation/automation-dsc-overview.md) para armazenar configurações centralmente e manter o estado desejado de computadores conectados híbridos habilitados por meio da extensão de VM de DSC.

- Coletar dados de log para análise com [logs no Azure monitor](../../azure-monitor/platform/data-platform-logs.md) habilitado por meio da extensão de VM do agente log Analytics. Isso é útil para executar análises complexas em dados de uma variedade de fontes.

- Com o [Azure monitor para VMs](../../azure-monitor/insights/vminsights-overview.md), o analisa o desempenho de suas VMs do Windows e do Linux e monitora seus processos e dependências em outros recursos e processos externos. Isso é obtido por meio da habilitação do agente de Log Analytics e das extensões de VM do agente de dependência.

- Baixe e execute scripts em computadores conectados híbridos usando a extensão de script personalizado. Essa extensão é útil para a configuração de pós-implantação, instalação de software ou qualquer outra configuração ou tarefas de gerenciamento.

## <a name="availability"></a>Disponibilidade

A funcionalidade de extensão de VM está disponível somente na lista de [regiões com suporte](overview.md#supported-regions). Certifique-se de carregar seu computador em uma dessas regiões.

## <a name="extensions"></a>Extensões

Nesta versão, damos suporte às seguintes extensões de VM em computadores Windows e Linux.

|Extensão |Sistema operacional |Publicador |Informações adicionais |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Extensão de script personalizado do Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Extensão DSC do Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agente do Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics extensão de VM para Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Extensão da máquina virtual do agente de dependência para Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Extensão de script personalizado do Linux versão 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Extensão de DSC do PowerShell para Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agente do Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics extensão de VM para Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Extensão da máquina virtual do agente de dependência para Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Para saber mais sobre o pacote do agente do computador conectado do Azure e detalhes sobre o componente do agente de extensão, consulte [visão geral do agente](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso depende dos seguintes provedores de recursos do Azure em sua assinatura:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se eles ainda não estiverem registrados, siga as etapas em [registrar provedores de recursos do Azure](agent-overview.md#register-azure-resource-providers).

A extensão de VM do agente de Log Analytics para Linux requer o Python 2. x instalado no computador de destino.

### <a name="connected-machine-agent"></a>Agente do Connected Machine

Verifique se seu computador corresponde às [versões com suporte](agent-overview.md#supported-operating-systems) do sistema operacional Windows e Linux para o agente do computador conectado do Azure.

A versão mínima do agente do computador conectado que tem suporte com esse recurso no Windows e no Linux é a versão 1,0.

Para atualizar seu computador para a versão do agente necessária, consulte [Atualizar agente](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Próximas etapas

Você pode implantar, gerenciar e remover extensões de VM usando o [CLI do Azure](manage-vm-extensions-cli.md), o [PowerShell](manage-vm-extensions-powershell.md), dos modelos [portal do Azure](manage-vm-extensions-portal.md)ou [Azure Resource Manager](manage-vm-extensions-template.md).
