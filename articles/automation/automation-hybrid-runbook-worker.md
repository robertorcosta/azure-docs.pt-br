---
title: Visão geral de Hybrid Runbook Worker na automação do Azure
description: Este artigo fornece uma visão geral do Hybrid Runbook Worker, que é um recurso da automação do Azure que você pode usar para executar runbooks em computadores em seu datacenter local ou provedor de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 0b36651a40267ec3ea8bfe7285c5f6c5d5c31562
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871647"
---
# <a name="hybrid-runbook-worker-overview"></a>Visão geral do Hybrid Runbook Worker

Os runbooks na Automação do Azure talvez não tenham acesso aos recursos em outras nuvens ou no seu ambiente local por serem executados na plataforma de nuvem do Azure. É possível usar o recurso Hybrid Runbook Worker da Automação do Azure para executar os runbooks diretamente no computador que hospeda a função e os recursos do ambiente para gerenciar esses recursos locais. Runbooks são armazenados e gerenciados na Automação do Azure e, em seguida, entregues a um ou mais computadores atribuídos.

A imagem a seguir ilustra essa funcionalidade:

![Visão geral do Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Um Hybrid Runbook Worker pode executar o sistema operacional Windows ou Linux. Para o monitoramento, ele requer o uso de Azure Monitor e um agente de Log Analytics para o sistema operacional com suporte. Para saber mais, consulte [Azure Monitor](automation-runbook-execution.md#azure-monitor).

Cada Runbook Worker Híbrido é membro de um grupo de Runbook Worker Híbrido que você especifica ao instalar o agente. Um grupo pode conter um único agente, mas você pode instalar vários agentes em um grupo para ter alta disponibilidade. Cada computador pode hospedar um relatório de trabalho híbrido para uma conta de automação. 

Quando você inicia um runbook em um Runbook Worker Híbrido, deve especificar o grupo no qual ele será executado. Cada operador no grupo de sonda de automação do Azure para ver se todos os trabalhos estão disponíveis. Se um trabalho estiver disponível, o primeiro funcionário a obter o trabalho o fará. O tempo de processamento da fila de trabalhos depende do perfil de hardware do Hybrid Worker e do carregamento. Você não pode especificar um trabalhador específico. 

Use um Hybrid Runbook Worker em vez de uma [área restrita do Azure](automation-runbook-execution.md#runbook-execution-environment) porque ele não tem muitos dos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) de área restrita em espaço em disco, memória ou soquetes de rede. Os limites em um Hybrid Worker estão relacionados somente aos recursos próprios do trabalho. 

> [!NOTE]
> Hybrid runbook Workers não são restritos pelo limite de tempo de [compartilhamento justo](automation-runbook-execution.md#fair-share) que as áreas restritas do Azure têm. 

## <a name="hybrid-runbook-worker-installation"></a>Instalação do Hybrid Runbook Worker

O processo para instalar uma Hybrid Runbook Worker depende do sistema operacional. A tabela a seguir define os tipos de implantação.

|Sistema operacional  |Tipo de Implantação  |
|---------|---------|
|Windows     | [Automatizado](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

O método de instalação recomendado é usar um runbook de automação do Azure para automatizar completamente o processo de configuração de um computador com Windows. O segundo método é seguir um procedimento passo a passo para instalar e configurar manualmente a função. Para máquinas Linux, você executa um script Python para instalar o agente na máquina.

## <a name="network-configuration"></a><a name="network-planning"></a>Configuração de rede

Para que o Hybrid Runbook Worker se conecte e se registre na automação do Azure, ele deve ter acesso ao número da porta e às URLs descritas nesta seção. O trabalhador também deve ter acesso às [portas e às URLs necessárias para que log Analytics Agent](../azure-monitor/platform/agent-windows.md) se conecte ao espaço de trabalho Azure monitor log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

As seguintes portas e URLs são necessárias para o Hybrid Runbook Worker:

* Porta: somente TCP 443 necessário para acesso de Internet de saída
* URL global: *.azure-automation.net
* URL global do EUA Gov Virgínia: *.azure automation.us
* Serviço de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

Recomendamos que você use os endereços listados ao definir [exceções](automation-runbook-execution.md#exceptions). Para endereços IP, você pode baixar os [intervalos de IP do Microsoft Azure datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

### <a name="dns-records-per-region"></a>Registros DNS por região

Se você tiver uma conta de automação definida para uma região específica, poderá restringir Hybrid Runbook Worker comunicação com esse datacenter regional. A tabela a seguir fornece o registro DNS para cada região.

| **Região** | **Registro DNS** |
| --- | --- |
| Austrália Central |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Leste da Austrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Norte da Europa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Sudeste da Ásia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gov. dos EUA – Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Oeste dos EUA 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

Para obter uma lista de endereços IP da região em vez de nomes da região, faça o download do arquivo XML do [Endereço IP do Centro de Dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) do Centro de Download da Microsoft. Um arquivo de endereço IP atualizado é lançado semanalmente. 

O arquivo de endereço IP lista os intervalos de endereços IP que são usados nos data centers Microsoft Azure. Ele inclui os intervalos de computação, SQL e armazenamento, e reflete os intervalos implantados no momento e quaisquer alterações futuras nos intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana.

É uma boa ideia baixar o novo arquivo de endereço IP toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. 

> [!NOTE]
> Se você estiver usando o Azure ExpressRoute, lembre-se de que o arquivo de endereço IP é usado para atualizar o anúncio de Border Gateway Protocol (BGP) do espaço do Azure na primeira semana de cada mês.

### <a name="proxy-server-use"></a>Uso do servidor proxy

Se você usar um servidor proxy para a comunicação entre a automação do Azure e o agente de Log Analytics, verifique se os recursos apropriados estão acessíveis. O tempo limite para solicitações dos serviços de Hybrid Runbook Worker e automação é de 30 segundos. Após três tentativas, uma solicitação falhará. 

### <a name="firewall-use"></a>Uso do firewall

Se você usar um firewall para restringir o acesso à Internet, deverá configurar o firewall para permitir o acesso. Se estiver usando o gateway de Log Analytics como um proxy, verifique se ele está configurado para Hybrid runbook Workers. Consulte [Configurar o gateway de log Analytics para trabalhos híbridos de automação](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Gerenciamento de Atualizações em Hybrid Runbook Worker

Quando a [Gerenciamento de atualizações](automation-update-management.md) de automação do Azure estiver habilitada, qualquer computador conectado ao seu espaço de trabalho do log Analytics será configurado automaticamente como um Hybrid runbook Worker. Cada operador pode dar suporte a runbooks direcionados ao gerenciamento de atualizações. 

Um computador configurado dessa maneira não está registrado em nenhum grupo de Hybrid Runbook Worker já definido em sua conta de automação. Você pode adicionar o computador a um grupo de Hybrid Runbook Worker, mas deve usar a mesma conta para Gerenciamento de Atualizações e a associação de grupo de Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Endereços de Gerenciamento de Atualizações para Hybrid Runbook Worker

Além dos endereços padrão e das portas que o Hybrid Runbook Worker requer, Gerenciamento de Atualizações precisa dos endereços na próxima tabela. A comunicação com esses endereços usa a porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="state-configuration-dsc-on-hybrid-runbook-worker"></a>Configuração de estado (DSC) no Hybrid Runbook Worker

Você pode executar o recurso de [configuração de estado (DSC)](automation-dsc-overview.md) em um Hybrid runbook Worker. Para gerenciar a configuração de servidores que dão suporte ao Hybrid Runbook Worker, você deve adicionar os servidores como nós DSC. Para obter mais informações sobre a integração, consulte [computadores integrados para gerenciamento por configuração de estado (DSC)](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks em um Hybrid Runbook Worker

Você pode ter runbooks que gerenciam recursos no computador local ou executados em recursos no ambiente local onde um Hybrid Runbook Worker é implantado. Nesse caso, você pode optar por executar seus runbooks no Hybrid Worker em vez de em uma conta de automação. Os Runbooks são executados em um Hybrid Runbook Worker são idênticos em uma estrutura que você executa na conta de automação. Consulte [executar runbooks em um Hybrid runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Trabalhos de Hybrid Runbook Worker

Hybrid Runbook Worker trabalhos executados na conta **sistema** local no Windows ou na [conta nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) no Linux. A automação do Azure manipula trabalhos em Hybrid runbook Workers de forma ligeiramente diferente dos trabalhos executados em áreas restritas do Azure. Consulte [ambiente de execução de runbook](automation-runbook-execution.md#runbook-execution-environment).

Se a máquina host Hybrid Runbook Worker for reinicializada, qualquer trabalho de runbook em execução será reiniciado desde o início ou do último ponto de verificação para runbooks de fluxo de trabalho do PowerShell. Depois que um trabalho de runbook é reiniciado mais de três vezes, ele é suspenso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de runbook para um Hybrid Runbook Worker

Como eles acessam recursos que não são do Azure, os runbooks em execução em um Hybrid Runbook Worker não podem usar o mecanismo de autenticação normalmente usado por runbooks Autenticando para recursos do Azure. Um runbook fornece sua própria autenticação para recursos locais ou configura a autenticação usando [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Você também pode especificar uma conta Executar como para fornecer um contexto de usuário para todos os runbooks.

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para saber como solucionar problemas de seus Hybrid runbook Workers, consulte [Solucionando problemas de Hybrid runbook Workers](troubleshoot/hybrid-runbook-worker.md#general).