---
title: Visão geral do Hybrid Runbook Worker da Automação do Azure
description: Este artigo fornece uma visão geral do Hybrid Runbook Worker, que você pode usar para executar runbooks em computadores no seu datacenter ou provedor de nuvem local.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9305d0d6443c923c680af0d5fafc58887dadb902
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835284"
---
# <a name="hybrid-runbook-worker-overview"></a>Visão geral do Hybrid Runbook Worker

Os runbooks na Automação do Azure talvez não tenham acesso aos recursos em outras nuvens ou no seu ambiente local por serem executados na plataforma de nuvem do Azure. É possível usar o recurso Hybrid Runbook Worker da Automação do Azure para executar os runbooks diretamente no computador que hospeda a função e os recursos do ambiente para gerenciar esses recursos locais. Runbooks são armazenados e gerenciados na Automação do Azure e, em seguida, entregues a um ou mais computadores atribuídos.

A imagem a seguir ilustra essa funcionalidade:

![Visão geral do Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Um Hybrid Runbook Worker pode executar o sistema operacional Windows ou Linux. Para o monitoramento, ele requer o uso do Azure Monitor e um agente do Log Analytics para o sistema operacional com suporte. Para saber mais, consulte [Azure Monitor](automation-runbook-execution.md#azure-monitor).

Cada Runbook Worker Híbrido é membro de um grupo de Runbook Worker Híbrido que você especifica ao instalar o agente. Um grupo pode conter um único agente, mas você pode instalar vários agentes em um grupo para ter alta disponibilidade. Cada computador pode hospedar um relatório de trabalho híbrido para uma conta da Automação. 

Quando você inicia um runbook em um Runbook Worker Híbrido, deve especificar o grupo no qual ele será executado. Cada operador no grupo de sonda de automação do Azure para ver se todos os trabalhos estão disponíveis. Se um trabalho estiver disponível, o primeiro funcionário a obter o trabalho o fará. O tempo de processamento da fila de tarefas depende do perfil e da carga do hardware do trabalho híbrido. Você não pode especificar um trabalhador específico. 

Use um Hybrid Runbook Worker em vez de uma [área restrita do Azure](automation-runbook-execution.md#runbook-execution-environment) porque não possui muitos dos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) da área restrita de proteção no espaço em disco, memória ou soquetes de rede. Os limites em um trabalho híbrido estão relacionados somente aos recursos próprios do trabalho. 

> [!NOTE]
> Os Hybrid Runbook Workers não são limitados pelo prazo de [compartilhamento justo](automation-runbook-execution.md#fair-share) que as áreas restritas do Azure têm. 

## <a name="hybrid-runbook-worker-installation"></a>Instalar Hybrid Runbook Worker

O processo para instalar o Hybrid Runbook Worker depende do sistema operacional. A tabela a seguir define os tipos de implantação.

|Sistema operacional  |Tipo de Implantação  |
|---------|---------|
|Windows     | [Automatizado](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

O método de instalação recomendado é usar um runbook da Automação do Azure para automatizar completamente o processo de configuração de um computador com Windows. O segundo método é seguir um procedimento passo a passo para instalar e configurar a função manualmente. Para máquinas Linux, você executa um script Python para instalar o agente na máquina.

## <a name="network-planning"></a><a name="network-planning"></a>Planejamento de rede

Para que o Hybrid Runbook Worker se conecte e se registre na Automação do Azure, ele deve ter acesso ao número da porta e às URLs descritas nesta seção. O trabalho também deve ter acesso às [portas e URLs necessárias para que o agente do Log Analytics](../azure-monitor/platform/agent-windows.md) se conecte ao workspace do Log Analytics do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A porta e URLs a seguir são necessárias para a função do Hybrid Runbook Worker:

* Porta: Somente a TCP 443 é necessária para acesso à Internet de saída
* URL global: *.azure-automation.net
* URL global do EUA Gov Virgínia: *.azure automation.us
* Serviço de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

Recomendamos que você use os endereços listados ao definir [exceções](automation-runbook-execution.md#exceptions). Para endereços IP, baixe os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

### <a name="dns-records-per-region"></a>Registros DNS por região

Se você tiver uma conta da Automação definida para uma região específica, você pode restringir a comunicação do Hybrid Runbook Worker para esse centro de dados regional. A tabela a seguir fornece o registro DNS para cada região.

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

Para obter uma lista de endereços IP da região em vez de nomes da região, faça o download do arquivo XML do [Endereço IP do Centro de Dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) do Centro de Download da Microsoft. Um arquivo de endereço IP atualizado é postado semanalmente. 

O arquivo de endereço IP lista os intervalos de endereços IP que são usados nos datacenters do Microsoft Azure. Isso inclui intervalos de computação, SQL e armazenamento, e reflete os intervalos atualmente implementados e quaisquer alterações futuras nos intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana.

É uma boa ideia fazer o download do novo arquivo de endereço IP toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. 

> [!NOTE]
> Se você estiver usando o Azure ExpressRoute, lembre-se de que o arquivo de endereço IP é usado para atualizar o anúncio BGP (Border Gateway Protocol) do espaço do Azure na primeira semana de cada mês.

### <a name="proxy-server-use"></a>Uso do servidor proxy

Se você usar um servidor proxy para comunicação entre o agente da Automação do Azure e o agente do Log Analytics, verifique se os recursos apropriados estão acessíveis. O tempo limite para solicitações dos serviços Hybrid Runbook Worker e Automação é de 30 segundos. Após três tentativas, a solicitação falha. 

### <a name="firewall-use"></a>Uso de firewall

Se você usar um firewall para restringir o acesso à Internet, precisará configurar o firewall para permitir o acesso. Se estiver usando o gateway do Log Analytics como proxy, verifique se ele está configurado para Hybrid Runbook Workers. Confira [Configurar o Gateway do Log Analytics para Hybrid Workers de Automação](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Gerenciamento de Atualizações no Hybrid Runbook Worker

Quando o [Gerenciamento de Atualizações](automation-update-management.md) da Automação do Azure está habilitado, qualquer computador conectado ao workspace do Log Analytics é automaticamente configurado como um Hybrid Runbook Worker. Cada trabalho pode oferecer suporte a runbooks direcionados ao gerenciamento de atualizações. 

Um computador configurado dessa maneira não está registrado com nenhum grupo Hybrid Runbook Worker já definido na sua conta da Automação. Você pode adicionar o computador a um grupo Hybrid Runbook Worker, mas você precisa usar a mesma conta para os membros do grupo Gerenciamento de Atualizações e Hybrid Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Endereços de Gerenciamento de Atualizações para Hybrid Runbook Worker

Além dos endereços e portas padrão exigidos pelo Hybrid Runbook Worker, o Gerenciamento de Atualizações precisa dos endereços na tabela a seguir. A comunicação com esses endereços usa a porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>State Configuration da Automação do Azure em um Hybrid Runbook Worker

Você pode executar o [State Configuration da Automação do Azure](automation-dsc-overview.md) em um Hybrid Runbook Worker. Para gerenciar a configuração de servidores que oferecem suporte ao Hybrid Runbook Worker, você deve adicionar os servidores como nós DSC. Confira [Habilitar computadores para gerenciamento pelo State Configuration da Automação do Azure](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks em um Hybrid Runbook Worker

Você pode ter runbooks que gerenciam recursos no computador local ou executam recursos no ambiente local em que um Hybrid Runbook Worker está implantado. Nesse caso, você pode optar por executar seus runbooks no trabalho híbrido em vez de em uma conta da Automação. Os runbooks executados em um Hybrid Runbook Worker têm estrutura idêntica àqueles executados na conta da Automação. Confira [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Trabalhos do Hybrid Runbook Worker

Os trabalhos do Hybrid Runbook Workers são executados na conta **Sistema** local no Windows ou na [conta nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) no Linux. A Automação do Azure manipula trabalhos em Hybrid Runbook Workers de maneira ligeiramente diferente dos trabalhos executados em áreas restritas do Azure. Confira [Ambiente de execução de runbook](automation-runbook-execution.md#runbook-execution-environment).

Se o computador host do Hybrid Runbook Worker reiniciar, qualquer trabalho de runbook em execução será reiniciado desde o início ou do último ponto de verificação para runbooks do Fluxo de trabalho do PowerShell. Se um trabalho do runbook for reiniciado mais de três vezes, será suspenso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de runbook para um Hybrid Runbook Worker

Como eles acessam recursos que não são do Azure, os runbooks em execução em um Hybrid Runbook Worker não podem usar o mecanismo de autenticação normalmente usado pelos runbooks que se autenticam nos recursos do Azure. Um runbook fornece sua própria autenticação aos recursos locais, ou configura a autenticação usando [entidades gerenciadas para os recursos do Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Você também pode especificar uma conta Executar como para fornecer um contexto de usuário a todos os runbooks.

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para saber como solucionar problemas de seus Hybrid Runbook Workers, confira [Solucionar Problemas do Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).