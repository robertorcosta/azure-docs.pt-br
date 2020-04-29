---
title: Visão geral da Configuração de Estado da Automação do Azure
description: Uma visão geral da DSC (configuração de estado da automação) do Azure, seus termos e problemas conhecidos
keywords: powershell dsc, configuração de estado desejada, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: afceb11180662416aa4953b8b58ef03ffaa70eec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406178"
---
# <a name="state-configuration-overview"></a>Visão geral de State Configuration

A configuração de estado da automação do Azure é um serviço do Azure que permite que você grave, gerencie e compile [configurações](/powershell/scripting/dsc/configurations/configurations)de DSC (configuração de estado desejado) do PowerShell. O serviço também importa [recursos de DSC](/powershell/scripting/dsc/resources/resources)e atribui configurações aos nós de destino, tudo na nuvem.

## <a name="why-use-azure-automation-state-configuration"></a>Por que usar a Configuração de Estado da Automação do Azure?

A Configuração do Estado de Automação do Azure oferece várias vantagens a usar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de pull interno

A configuração de estado da automação do Azure fornece um servidor de pull de DSC semelhante ao [recurso do Windows DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). Os nós de destino podem receber configurações automaticamente, obedecer ao estado desejado e relatar sua conformidade. O servidor de pull interno na Automação do Azure elimina a necessidade de configurar e manter seu próprio servidor de pull. A Automação do Azure pode destinar computadores Windows ou Linux físicos ou virtuais, na nuvem ou localmente.

### <a name="management-of-all-your-dsc-artifacts"></a>Gerenciamento de todos os seus artefatos de DSC

A configuração de estado da automação do Azure traz a mesma camada de gerenciamento para a [configuração de estado desejado do PowerShell](/powershell/scripting/dsc/overview/overview) , pois ela oferece para scripts do PowerShell. No portal do Azure ou do PowerShell, você pode gerenciar todas as suas configurações de DSC, recursos e nós de destino.

![Captura de tela da página de Automação do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importação de dados de relatórios em logs de Azure Monitor

Nós gerenciados com a Configuração do Estado de Automação do Azure enviam dados de status de relatórios detalhados para o servidor de pull interno. É possível configurar a Configuração do Estado de Automação do Azure para enviar esses dados ao espaço de trabalho do Log Analytics. Consulte [encaminhar dados de relatório de configuração de estado da automação do Azure para Azure monitor logs](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Pré-requisitos para usar a configuração de estado da automação do Azure

Considere os seguintes requisitos ao usar a configuração de estado de automação do Azure para DSC.

### <a name="operating-system-requirements"></a>Requisitos do sistema operacional

Para nós que executam o Windows, há suporte para as seguintes versões:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>O SKU do produto autônomo do [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) não contém uma implementação de DSC. Portanto, ele não pode ser gerenciado pela configuração de estado da automação do PowerShell ou do Azure.

Para nós que executam o Linux, a extensão de DSC do Linux dá suporte a todas as distribuições do Linux listadas em [distribuições do Linux com suporte](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisitos de DSC

Para todos os nós do Windows em execução no Azure, o [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) é instalado durante a integração. Para nós que executam o Windows Server 2012 e o Windows 7, o [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) está habilitado.

Para todos os nós do Linux em execução no Azure, o [PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) é instalado durante a integração.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuração de redes privadas

Se os nós estiverem localizados em uma rede privada, a porta e as URLs a seguir serão necessárias. Esses recursos fornecem conectividade de rede para o nó gerenciado e permitem que a DSC se comunique com a automação do Azure.

* Porta: somente TCP 443 necessário para acesso de Internet de saída
* URL global: ***. Azure-Automation.net**
* URL global de US Gov-Virgínia: ***. Azure-Automation.us**
* Serviço do agente **:\<https://workspaceid\>. AgentSvc.Azure-Automation.net**

Se você estiver usando recursos de DSC que se comunicam entre nós, como os [recursos WAITFOR *](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), também precisará permitir o tráfego entre os nós. Consulte a documentação de cada recurso de DSC para entender esses requisitos de rede.

#### <a name="proxy-support"></a>Suporte a proxy

O suporte de proxy para o agente DSC está disponível no Windows versão 1809 e posterior. Essa opção é habilitada definindo os valores de `ProxyURL` e `ProxyCredential` no [script de metaconfiguração](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) usado para registrar nós.

>[!NOTE]
>A configuração de estado da automação do Azure não fornece suporte a proxy de DSC para versões anteriores do Windows.

Para nós do Linux, o agente de DSC oferece suporte a `http_proxy` proxy e usa a variável para determinar a URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Namespace e intervalos de rede de configuração de estado da automação do Azure

É recomendável usar os endereços listados abaixo ao definir exceções. Para endereços IP, você pode baixar os [intervalos de IP do Microsoft Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Se você tiver uma conta de Automação do Azure definida para uma região específica, você pode restringir a comunicação para esse centro de dados regional. A tabela a seguir fornece o registro DNS para cada região:

| **Região** | **Registro DNS** |
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

- Para começar a usar o DSC na configuração de estado da automação do Azure, consulte [introdução à configuração de estado da automação do Azure](automation-dsc-getting-started.md).
- Para saber como integrar nós, confira [máquinas de integração para o gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md).
- Para saber mais sobre como compilar configurações de DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, consulte [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md).
