---
title: Visão geral da Configuração de Estado da Automação do Azure
description: Este artigo é uma visão geral da Configuração do Estado de Automação do Azure (DSC), seus termos e seus problemas conhecidos.
keywords: powershell dsc, configuração de estado desejada, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1166f5a1d7586c54255120a656b060c93f842fd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406178"
---
# <a name="state-configuration-overview"></a>Visão geral de State Configuration

A configuração do estado de automação do Azure é um serviço do Azure que permite gravar, gerenciar e compilar [configurações](/powershell/scripting/dsc/configurations/configurations)de configuração do estado desejado (DSC) do PowerShell . O serviço também importa [recursos dSC](/powershell/scripting/dsc/resources/resources) e atribui configurações a nós de destino, tudo na nuvem.

## <a name="why-use-azure-automation-state-configuration"></a>Por que usar a State Configuration da Automação do Azure?

A Configuração do Estado de Automação do Azure oferece várias vantagens a usar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de pull interno

A configuração do estado de automação do Azure fornece um servidor de tração DSC semelhante ao [Recurso DSC-Service](/powershell/scripting/dsc/pull-server/pullserver)do Windows . Os nós-alvo podem receber configurações automaticamente, estar em conformidade com o estado desejado e informar sobre sua conformidade. O servidor de pull interno na Automação do Azure elimina a necessidade de configurar e manter seu próprio servidor de pull. A Automação do Azure pode destinar computadores Windows ou Linux físicos ou virtuais, na nuvem ou localmente.

### <a name="manage-all-your-dsc-artifacts"></a>Gerencie todos os seus artefatos DSC

A configuração do estado de automação do Azure traz a mesma camada de gerenciamento para a [configuração de estado desejado do PowerShell](/powershell/scripting/dsc/overview/overview) que oferece para scripting PowerShell. A partir do portal Azure ou do PowerShell, você pode gerenciar todas as configurações, recursos e nós de destino do DSC.

![Captura de tela da página de Automação do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importar dados de relatórios para logs do Monitor do Azure

Nós gerenciados com a Configuração do Estado de Automação do Azure enviam dados de status de relatórios detalhados para o servidor de pull interno. É possível configurar a Configuração do Estado de Automação do Azure para enviar esses dados ao espaço de trabalho do Log Analytics. Para obter mais informações, consulte [Forward Azure Automation State Configuration reporting data to Azure Monitor Logs](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes requisitos ao usar a configuração do estado de automação do Azure para DSC.

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
>Como o Produto Autônomo [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) SKU não contém uma implementação do DSC, ele não pode ser gerenciado pelo PowerShell DSC ou a Configuração do Estado de Automação do Azure.

Para nós que executam o Linux, a extensão DSC Linux suporta todas as distribuições Linux listadas em [Distribuições Linux suportadas](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisitos do DSC

Para todos os nós do Windows em execução no Azure, [o Windows Management Framework 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) é instalado durante o onboarding. Para os nós que executam o Windows Server 2012 e o Windows 7, [o WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) está habilitado.

Para todos os nós Linux que são executados no Azure, [o PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) é instalado durante o onboarding.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuração de redes privadas

Se seus nós estiverem localizados em uma rede privada, as seguintes portas e URLs serão necessárias. Esses recursos fornecem conectividade de rede para o nó gerenciado e permitem que o DSC se comunique com a Azure Automation.

* Porta: Apenas o TCP 443 necessário para acesso à internet de saída
* URL global: ***.azure-automation.net**
* URL global do US Gov Virginia: ***.azure-automation.us**
* Serviço do agente: **\<https://\>workspaceId .agentsvc.azure-automation.net**

Se você estiver usando recursos DSC que se comunicam entre nós, como os [recursos WaitFor*,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)você também precisa permitir a comunicação entre nós. Para entender esses requisitos de rede, consulte a documentação de cada recurso do DSC.

#### <a name="proxy-support"></a>Suporte proxy

O suporte proxy para o agente DSC está disponível na versão 1809 do Windows e posterior. Você habilita essa opção definindo os valores para `ProxyURL` e `ProxyCredential` no script de [metaconfiguração](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) usado para registrar nomes.

>[!NOTE]
>A configuração do estado de automação do Azure não fornece suporte a proxy DSC para versões anteriores do Windows.

Para nós Linux, o agente DSC suporta um `http_proxy` servidor proxy e usa a variável para especificar a URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure Automation State Configuration Configuration ranges e namespace

Quando você estiver definindo exceções, recomendamos que você use os endereços IP listados na tabela a seguir. Para endereços IP, você pode baixar o arquivo [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) XML do Microsoft Download Center. Este arquivo contém os intervalos atualmente implantados e quaisquer alterações próximas nas faixas IP. É atualizado semanalmente.

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

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento.
>
>Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana. É uma boa idéia baixar um novo arquivo XML toda semana. Em seguida, você pode atualizar seu site para identificar corretamente os serviços em execução no Azure. 

Se você é um usuário do Azure ExpressRoute, observe que este arquivo é usado para atualizar o anúncio do Border Gateway Protocol (BGP) do espaço Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o DSC na configuração do estado de automação do Azure, consulte [Comece com a configuração do estado de automação do Azure](automation-dsc-getting-started.md).
- Para aprender como a bordo de nódulos, consulte [máquinas a bordo para gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md).
- Para saber mais sobre a compilação das configurações do DSC para que você possa atribuí-las a nós de destino, consulte [Compilar configurações na configuração do estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Para obter informações sobre preços, consulte os preços de [configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para um exemplo de usar a configuração do estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua em máquinas virtuais usando a configuração do estado de automação do Azure e chocolatey](automation-dsc-cd-chocolatey.md).
