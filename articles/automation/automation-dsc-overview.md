---
title: Visão geral da Configuração de Estado da Automação do Azure
description: Uma visão geral da Configuração do Estado de Automação do Azure (DSC), seus termos e problemas conhecidos
keywords: powershell dsc, configuração de estado desejada, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 46cf0d6a12ffbc836db7bd79c0f2738a94e23085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283184"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral da Configuração de Estado da Automação do Azure

A configuração do estado de automação do Azure é um serviço do Azure que permite gravar, gerenciar e compilar [configurações](/powershell/scripting/dsc/configurations/configurations)de configuração do estado desejado (DSC) do PowerShell . O serviço também importa [recursos DSC](/powershell/scripting/dsc/resources/resources), e atribui configurações a nós de destino, tudo na nuvem.

## <a name="why-use-azure-automation-state-configuration"></a>Por que usar a Configuração de Estado da Automação do Azure?

A Configuração do Estado de Automação do Azure oferece várias vantagens a usar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de pull interno

A configuração do estado de automação do Azure fornece um servidor de tração DSC semelhante ao [Recurso DSC-Service](/powershell/scripting/dsc/pull-server/pullserver)do Windows . Os nós-alvo podem receber configurações automaticamente, estar em conformidade com o estado desejado e informar sobre sua conformidade. O servidor de pull interno na Automação do Azure elimina a necessidade de configurar e manter seu próprio servidor de pull. A Automação do Azure pode destinar computadores Windows ou Linux físicos ou virtuais, na nuvem ou localmente.

### <a name="management-of-all-your-dsc-artifacts"></a>Gerenciamento de todos os seus artefatos de DSC

A configuração do estado de automação do Azure traz a mesma camada de gerenciamento para a [configuração de estado desejado do PowerShell](/powershell/scripting/dsc/overview/overview) que oferece para scripting PowerShell. A partir do portal Azure ou do PowerShell, você pode gerenciar todas as configurações, recursos e nós de destino do DSC.

![Captura de tela da página de Automação do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importação de dados de relatórios em logs do Azure Monitor

Nós gerenciados com a Configuração do Estado de Automação do Azure enviam dados de status de relatórios detalhados para o servidor de pull interno. É possível configurar a Configuração do Estado de Automação do Azure para enviar esses dados ao espaço de trabalho do Log Analytics. Consulte [O relatório de configuração do estado de automação do Azure forward para logs do Monitor do Azure](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Pré-requisitos para usar a configuração do estado de automação do Azure

Por favor, considere os seguintes requisitos ao usar a configuração do estado de automação do Azure para DSC.

### <a name="operating-system-requirements"></a>Requisitos do sistema operacional

Para nós que executam o Windows, as seguintes versões são suportadas:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>O Produto Autônomo [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) SKU não contém uma implementação do DSC. Assim, ele não pode ser gerenciado pelo PowerShell DSC ou a Azure Automation State Configuration.

Para nós que executam o Linux, a extensão DSC Linux suporta todas as distribuições Linux listadas em [Distribuições Linux suportadas](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisitos do DSC

Para todos os nós do Windows em execução no Azure, [o WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) é instalado durante o onboarding. Para os nós que executam o Windows Server 2012 e o Windows 7, [o WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) está habilitado.

Para todos os nós Linux que são executados no Azure, [o PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) é instalado durante o onboarding.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuração de redes privadas

Se seus nós estiverem localizados em uma rede privada, as seguintes portas e URLs serão necessárias. Esses recursos fornecem conectividade de rede para o nó gerenciado e permitem que o DSC se comunique com a Azure Automation.

* Porta: Apenas o TCP 443 necessário para acesso à internet de saída
* URL global: ***.azure-automation.net**
* URL global do US Gov Virginia: ***.azure-automation.us**
* Serviço do agente: **\<https://\>workspaceId .agentsvc.azure-automation.net**

Se você estiver usando recursos DSC que se comunicam entre nós, como os [recursos WaitFor*,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)você também precisa permitir o tráfego entre nós. Consulte a documentação de cada recurso do DSC para entender esses requisitos de rede.

#### <a name="proxy-support"></a>Suporte proxy

O suporte proxy para o agente DSC está disponível na versão 1809 do Windows e posterior. Essa opção é habilitada definindo os valores para `ProxyURL` e `ProxyCredential` no script de [metaconfiguração](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) usado para registrar nomes.

>[!NOTE]
>A configuração do estado de automação do Azure não fornece suporte a proxy DSC para versões anteriores do Windows.

Para nós Linux, o agente DSC suporta `http_proxy` proxy e usa a variável para determinar a URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure Automation State Configuration Configuration ranges e namespace

Recomenda-se usar os endereços listados abaixo ao definir exceções. Para endereços IP, você pode baixar as faixas IP do [Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Se você tiver uma conta de Automação do Azure definida para uma região específica, você pode restringir a comunicação para esse centro de dados regional. A tabela a seguir fornece o registro DNS para cada região:

| **Região** | **Registro de DNS** |
| --- | --- |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Leste dos EUA    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Norte da Europa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste da Ásia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gov. dos EUA – Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes da região, faça o download do arquivo XML do [Endereço IP do Centro de Dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) do Centro de Download da Microsoft.

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento.
>
>Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana. É uma boa ideia fazer o download do novo arquivo XML toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. 

Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio BGP (Border Gateway Protocol) do espaço do Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o DSC na configuração do estado de automação do Azure, consulte [Como começar com a configuração do estado de automação do Azure](automation-dsc-getting-started.md).
- Para aprender como a bordo de nódulos, consulte [máquinas de onboarding para gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md).
- Para saber mais sobre a compilação das configurações do DSC para que você possa atribuí-las a nós de destino, consulte [Compilando configurações na configuração do estado de automação do Azure](automation-dsc-compile.md).
- Para obter referência de cmdlet PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation).
- Para obter informações sobre preços, consulte os preços de [configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de uso da configuração do estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando a configuração do estado de automação do Azure e chocolatey](automation-dsc-cd-chocolatey.md).
