---
title: Gerenciamento de extensão de VM com servidores habilitados para Arc do Azure
description: Os servidores habilitados para Arc do Azure podem gerenciar a implantação de extensões de máquina virtual que fornecem tarefas de automação e configuração pós-implantação com VMs não Azure.
ms.date: 12/14/2020
ms.topic: conceptual
ms.openlocfilehash: 55e21f9c6bcd2dfe5f995093034773f2a87d9b03
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504501"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gerenciamento de extensão de máquina virtual com servidores habilitados para Arc do Azure

As extensões de VM (máquina virtual) são pequenos aplicativos que fornecem tarefas de configuração e automação de pós-implantação em VMs do Azure. Por exemplo, se uma máquina virtual exigir instalação de software, proteção antivírus ou para executar um script nele, uma extensão de VM poderá ser usada.

Os servidores habilitados para Arc do Azure permitem implantar extensões de VM do Azure em VMs não Azure Windows e Linux, simplificando o gerenciamento da sua máquina híbrida por meio de seu ciclo de vida. As extensões de VM podem ser gerenciadas usando os seguintes métodos em seus computadores híbridos ou servidores gerenciados por servidores habilitados para Arc:

- O [Portal do Azure](manage-vm-extensions-portal.md)
- A [CLI do Azure](manage-vm-extensions-cli.md)
- [PowerShell do Azure](manage-vm-extensions-powershell.md)
- Modelos do Azure [Resource Manager](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Principais benefícios

O suporte à extensão de VM dos servidores habilitados para Arc do Azure oferece os seguintes benefícios principais:

- Use a [configuração de estado da automação do Azure](../../automation/automation-dsc-overview.md) para armazenar configurações centralmente e manter o estado desejado de computadores conectados híbridos habilitados por meio da extensão de VM de DSC.

- Coletar dados de log para análise com [logs no Azure monitor](../../azure-monitor/platform/data-platform-logs.md) habilitado por meio da extensão de VM do agente log Analytics. Isso é útil para fazer análises complexas em dados de diferentes tipos de fontes.

- Com o [Azure monitor para VMs](../../azure-monitor/insights/vminsights-overview.md), o analisa o desempenho de suas VMs do Windows e do Linux e monitora seus processos e dependências em outros recursos e processos externos. Isso é obtido por meio da habilitação do agente de Log Analytics e das extensões de VM do agente de dependência.

- Baixe e execute scripts em computadores conectados híbridos usando a extensão de script personalizado. Essa extensão é útil para a configuração de pós-implantação, instalação de software ou qualquer outra configuração ou tarefas de gerenciamento.

- Atualização automática de certificados armazenados em um [Azure Key Vault](../../key-vault/general/overview.md).

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
|Key Vault | Windows | Microsoft.Compute | [Extensão da máquina virtual de Key Vault para Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|CustomScript|Linux |Microsoft. Azure. Extension |[Extensão de script personalizado do Linux versão 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Extensão de DSC do PowerShell para Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agente do Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics extensão de VM para Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Extensão da máquina virtual do agente de dependência para Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Key Vault | Linux | Microsoft.Compute | [Extensão da máquina virtual de Key Vault para Linux](../../virtual-machines/extensions/key-vault-linux.md) |

Para saber mais sobre o pacote do agente do computador conectado do Azure e detalhes sobre o componente do agente de extensão, consulte [visão geral do agente](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso depende dos seguintes provedores de recursos do Azure em sua assinatura:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se eles ainda não estiverem registrados, siga as etapas em [registrar provedores de recursos do Azure](agent-overview.md#register-azure-resource-providers).

Certifique-se de examinar a documentação de cada extensão de VM referenciada na tabela anterior para entender se ela tem requisitos de rede ou de sistema. Isso pode ajudá-lo a evitar problemas de conectividade com um serviço ou recurso do Azure que dependa dessa extensão de VM.

### <a name="log-analytics-vm-extension"></a>Log Analytics extensão de VM

A extensão de VM do agente de Log Analytics para Linux requer o Python 2. x instalado no computador de destino. 

### <a name="azure-key-vault-vm-extension-preview"></a>Extensão de VM Azure Key Vault (versão prévia)

A extensão de VM Key Vault (versão prévia) não dá suporte aos seguintes sistemas operacionais Linux:

- CentOS Linux 7 (x64)
- RHEL (Red Hat Enterprise Linux) 7 (x64)
- Amazon Linux 2 (x64)

A implantação da extensão de VM Key Vault (visualização) só tem suporte usando:

- A CLI do Azure
- O Azure PowerShell
- Modelo do Azure Resource Manager

Antes de implantar a extensão, você precisará concluir o seguinte:

1. [Crie um cofre e um certificado](../../key-vault/certificates/quick-create-portal.md) (autoassinado ou importação).

2. Conceda ao Azure Arc acesso do servidor habilitado para o segredo do certificado. Se você estiver usando a [visualização do RBAC](../../key-vault/general/rbac-guide.md), procure o nome do recurso de arco do Azure e atribua a ele a função de **usuário Key Vault segredos (versão prévia)** . Se você estiver usando a [política de acesso Key Vault](../../key-vault/general/assign-access-policy-portal.md), atribua permissões de **Get** de segredo à identidade atribuída do sistema do recurso de arco do Azure.

### <a name="connected-machine-agent"></a>Agente do Connected Machine

Verifique se seu computador corresponde às [versões com suporte](agent-overview.md#supported-operating-systems) do sistema operacional Windows e Linux para o agente do computador conectado do Azure.

A versão mínima do agente do computador conectado que tem suporte com esse recurso no Windows e no Linux é a versão 1,0.

Para atualizar seu computador para a versão do agente necessária, consulte [Atualizar agente](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Próximas etapas

Você pode implantar, gerenciar e remover extensões de VM usando o [CLI do Azure](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), dos modelos [portal do Azure](manage-vm-extensions-portal.md)ou [Azure Resource Manager](manage-vm-extensions-template.md).
