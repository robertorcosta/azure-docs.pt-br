---
title: Visão geral da Configuração de Estado da Automação do Azure
description: Uma visão geral da Configuração do Estado de Automação do Azure (DSC), seus termos e problemas conhecidos
keywords: powershell dsc, configuração de estado desejada, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5ed18f8a8bbd8bd323dec54ca3f700c7ce168dde
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231640"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral da Configuração de Estado da Automação do Azure

A Configuração do Estado de Automação do Azure é um serviço do Azure que permite gravar, gerenciar e compilar [configurações](/powershell/scripting/dsc/configurations/configurations) de DSC (Desired State Configuration) do PowerShell, importar [recursos de DSC](/powershell/scripting/dsc/resources/resources) e atribuir configurações a nós de destino, tudo na nuvem.

## <a name="why-use-azure-automation-state-configuration"></a>Por que usar a Configuração de Estado da Automação do Azure?

A Configuração do Estado de Automação do Azure oferece várias vantagens a usar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de pull interno

A Configuração do Estado Automação do Azure fornece um servidor de recepção DSC semelhante ao [Serviço de DSC do Recurso do Windows](/powershell/scripting/dsc/pull-server/pullserver) para que os nós de destino recebam automaticamente configurações, estejam em conformidade com o estado desejado e relatem sua conformidade. O servidor de pull interno na Automação do Azure elimina a necessidade de configurar e manter seu próprio servidor de pull. A Automação do Azure pode destinar computadores Windows ou Linux físicos ou virtuais, na nuvem ou localmente.

### <a name="management-of-all-your-dsc-artifacts"></a>Gerenciamento de todos os seus artefatos de DSC

A Configuração do Estado de Automação do Azure oferece a mesma camada de gerenciamento para a [Desired State Configuration do PowerShell](/powershell/scripting/dsc/overview/overview) que a Automação do Azure oferece para scripts do PowerShell.

No portal do Azure ou do PowerShell, você pode gerenciar todas as suas configurações, recursos e nós de destino da DSC.

![Captura de tela da página de Automação do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Import reporting data into Azure Monitor logs

Nós gerenciados com a Configuração do Estado de Automação do Azure enviam dados de status de relatórios detalhados para o servidor de pull interno. É possível configurar a Configuração do Estado de Automação do Azure para enviar esses dados ao espaço de trabalho do Log Analytics. To learn how to send State Configuration status data to your Log Analytics workspace, see [Forward Azure Automation State Configuration reporting data to Azure Monitor logs](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Please consider the following requirements when using Azure Automation State Configuration (DSC).

### <a name="operating-system-requirements"></a>Operating System Requirements

For nodes running Windows, the following versions are supported:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

The [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) standalone product sku does not contain an implementation of Desired State Configuraion so it cannot be managed by PowerShell DSC or Azure Automation State Configuration.

For nodes running Linux, the following distros/versions are supported:

The DSC Linux extension supports all the Linux distributions listed under [Supported Linux Distributions](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>DSC requirements

For all Windows nodes running in Azure, [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) will be installed during onboarding.  For nodes running Windows Server 2012 and Windows 7, [WinRM will be enabled](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

For all Linux nodes running in Azure, [PowerShell DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) will be installed during onboarding.

### <a name="network-planning"></a>Configure private networks

If your nodes are located within a private network, the following port and URLs are required for State Configuration (DSC) to communicate with Automation:

* Porta: Somente a TCP 443 é necessária para acesso de Internet de saída.
* URL global: *.azure-automation.net
* URL global do EUA Gov Virgínia: *.azure automation.us
* Serviço de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

This provides network connectivity for the managed node to communicate with Azure Automation.
If you are using DSC resources that communicate between nodes, such as the [WaitFor* resources](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), you will also need to allow traffic between nodes.
See the documentation for each DSC resource to understand those network requirements.

#### <a name="proxy-support"></a>Proxy Support

Proxy support for the DSC agent is available in Windows version 1809 and later.
To configure this option, set the value for **ProxyURL** and **ProxyCredential** in the [metaconfiguration script](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) used to register nodes.
Proxy is not available in DSC for previous versions of Windows.

For Linux nodes, the DSC agent supports proxy and will utilize the http_proxy variable to determine the url.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure State Configuration network ranges and namespace

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, baixe os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Se você tiver uma conta de Automação do Azure definida para uma região específica, você pode restringir a comunicação para esse centro de dados regional. A tabela a seguir fornece o registro DNS para cada região:

| **Região** | **Registro DNS** |
| --- | --- |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Centro-Sul dos EUA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Leste dos EUA   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Oeste da Europa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa Setentrional |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste Asiático |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov - Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes da região, faça o download do arquivo XML do [Endereço IP do Centro de Dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) do Centro de Download da Microsoft.

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento.
>
>Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana.
>
> É uma boa ideia fazer o download do novo arquivo XML toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio BGP (Border Gateway Protocol) do espaço do Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Próximos passos

- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber mai sobre nós de integração, veja [Máquinas de integração para o gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
