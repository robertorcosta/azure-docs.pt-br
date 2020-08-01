---
title: Visão geral do Hybrid Runbook Worker da Automação do Azure
description: Este artigo fornece uma visão geral do Hybrid Runbook Worker, que você pode usar para executar runbooks em computadores no seu datacenter ou provedor de nuvem local.
services: automation
ms.subservice: process-automation
ms.date: 07/16/2020
ms.topic: conceptual
ms.openlocfilehash: 4d29979e28140b728478d405db934cb41783f4b0
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448084"
---
# <a name="hybrid-runbook-worker-overview"></a>Visão geral do Hybrid Runbook Worker

Os runbooks na Automação do Azure talvez não tenham acesso aos recursos em outras nuvens ou no seu ambiente local por serem executados na plataforma de nuvem do Azure. Você pode usar o recurso Hybrid Runbook Worker da automação do Azure para executar runbooks diretamente no computador que está hospedando a função e em recursos no ambiente para gerenciar esses recursos locais. Os Runbooks são armazenados e gerenciados na automação do Azure e entregues a um ou mais computadores atribuídos.

A imagem a seguir ilustra essa funcionalidade:

![Visão geral do Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Um Hybrid Runbook Worker pode ser executado no sistema operacional Windows ou Linux. Depende do agente de [log Analytics](../azure-monitor/platform/log-analytics-agent.md) reportando para um [espaço de trabalho Azure monitor log Analytics](../azure-monitor/platform/design-logs-deployment.md). O espaço de trabalho não é apenas para monitorar o computador para o sistema operacional com suporte, mas também para baixar os componentes necessários para o Hybrid Runbook Worker.

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

O método de instalação recomendado é usar um runbook de automação do Azure para automatizar completamente o processo de configuração de um computador Windows. Se isso não for viável, você poderá seguir um procedimento passo a passo para instalar e configurar manualmente a função. Para máquinas Linux, você executa um script Python para instalar o agente na máquina.

## <a name="network-planning"></a><a name="network-planning"></a>Planejamento de rede

Para que o Hybrid Runbook Worker se conecte e se registre na Automação do Azure, ele deve ter acesso ao número da porta e às URLs descritas nesta seção. O trabalho também deve ter acesso às [portas e URLs necessárias para que o agente do Log Analytics](../azure-monitor/platform/agent-windows.md) se conecte ao workspace do Log Analytics do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A porta e URLs a seguir são necessárias para a função do Hybrid Runbook Worker:

* Porta: Somente a TCP 443 é necessária para acesso à Internet de saída
* URL global:`*.azure-automation.net`
* URL global de US Gov-Virgínia:`*.azure-automation.us`
* Serviço do agente:`https://<workspaceId>.agentsvc.azure-automation.net`

Se você tiver uma conta da Automação definida para uma região específica, você pode restringir a comunicação do Hybrid Runbook Worker para esse centro de dados regional. Examine os [registros DNS usados pela automação do Azure](how-to/automation-region-dns-records.md) para os registros DNS necessários.

### <a name="proxy-server-use"></a>Uso do servidor proxy

Se você usar um servidor proxy para a comunicação entre a automação do Azure e as máquinas que executam o agente de Log Analytics, verifique se os recursos apropriados estão acessíveis. O tempo limite para solicitações dos serviços Hybrid Runbook Worker e Automação é de 30 segundos. Após três tentativas, a solicitação falha.

### <a name="firewall-use"></a>Uso de firewall

Se você usar um firewall para restringir o acesso à Internet, precisará configurar o firewall para permitir o acesso. Se estiver usando o gateway do Log Analytics como proxy, verifique se ele está configurado para Hybrid Runbook Workers. Confira [Configurar o Gateway do Log Analytics para Hybrid Workers de Automação](../azure-monitor/platform/gateway.md).

### <a name="service-tags"></a>Marcas de serviço

A automação do Azure dá suporte a marcas de serviço de rede virtual do Azure, começando com a marca de serviço [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](../virtual-network/security-overview.md#security-rules) ou no [Firewall do Azure](../firewall/service-tags.md). As marcas de serviço podem ser usadas no lugar de endereços IP específicos quando você cria regras de segurança. Ao especificar o nome da marca de serviço **GuestAndHybridManagement** no campo de origem ou de destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço de automação. Essa marca de serviço não dá suporte à permissão de controle mais granular restringindo intervalos de IP para uma região específica.

A marca de serviço para o serviço de automação do Azure fornece somente IPs usados para os seguintes cenários:

* Disparar WebHooks de dentro de sua rede virtual
* Permitir que os Hybrid runbook Workers ou os agentes de configuração de estado em sua VNet se comuniquem com o serviço de automação

>[!NOTE]
>A marca de serviço **GuestAndHybridManagement** atualmente não dá suporte à execução de trabalho de runbook em uma área restrita do Azure, somente diretamente em um Hybrid runbook Worker.

## <a name="support-for-impact-level-5-il5"></a>Suporte para impacto nível 5 (IL5)

A Hybrid Runbook Worker de automação do Azure pode ser usada no Azure governamental para dar suporte a cargas de trabalho de impacto nível 5 em uma das duas seguintes configurações:

* [Máquina virtual isolada](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Quando implantados, eles consomem todo o host físico para essa VM, fornecendo o nível necessário de isolamento necessário para dar suporte a cargas de trabalho do IL5.

* Os [hosts dedicados do Azure](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts), que fornecem servidores físicos capazes de hospedar uma ou mais máquinas virtuais, dedicados a uma assinatura do Azure.

>[!NOTE]
>O isolamento de computação por meio da função Hybrid Runbook Worker está disponível para nuvens comerciais do Azure e do governo dos EUA. 

## <a name="update-management-on-hybrid-runbook-worker"></a>Gerenciamento de Atualizações no Hybrid Runbook Worker

Quando a [Gerenciamento de atualizações](update-management/update-mgmt-overview.md) de automação do Azure estiver habilitada, qualquer computador conectado ao seu espaço de trabalho do log Analytics será configurado automaticamente como um Hybrid runbook Worker. Cada trabalho pode oferecer suporte a runbooks direcionados ao gerenciamento de atualizações.

Uma máquina configurada dessa maneira não está registrada em nenhum grupo de Hybrid Runbook Worker já definido em sua conta de automação. Você pode adicionar o computador a um grupo de Hybrid Runbook Worker, mas deve usar a mesma conta para Gerenciamento de Atualizações e a associação de grupo de Hybrid Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Endereços de Gerenciamento de Atualizações para Hybrid Runbook Worker

Além dos endereços e portas padrão necessários para o Hybrid Runbook Worker, Gerenciamento de Atualizações tem requisitos de configuração de rede adicionais descritos na seção [planejamento de rede](update-management/update-mgmt-overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>State Configuration da Automação do Azure em um Hybrid Runbook Worker

Você pode executar o [State Configuration da Automação do Azure](automation-dsc-overview.md) em um Hybrid Runbook Worker. Para gerenciar a configuração de servidores que oferecem suporte ao Hybrid Runbook Worker, você deve adicionar os servidores como nós DSC. Confira [Habilitar computadores para gerenciamento pelo State Configuration da Automação do Azure](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks em um Hybrid Runbook Worker

Você pode ter runbooks que gerenciam recursos no computador local ou executados em recursos no ambiente local onde um Hybrid Runbook Worker é implantado. Nesse caso, você pode optar por executar seus runbooks no trabalho híbrido em vez de em uma conta da Automação. Os runbooks executados em um Hybrid Runbook Worker têm estrutura idêntica àqueles executados na conta da Automação. Confira [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Trabalhos do Hybrid Runbook Worker

Os trabalhos do Hybrid Runbook Workers são executados na conta **Sistema** local no Windows ou na [conta nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) no Linux. A Automação do Azure manipula trabalhos em Hybrid Runbook Workers de maneira ligeiramente diferente dos trabalhos executados em áreas restritas do Azure. Confira [Ambiente de execução de runbook](automation-runbook-execution.md#runbook-execution-environment).

Se o computador host do Hybrid Runbook Worker reiniciar, qualquer trabalho de runbook em execução será reiniciado desde o início ou do último ponto de verificação para runbooks do Fluxo de trabalho do PowerShell. Se um trabalho do runbook for reiniciado mais de três vezes, será suspenso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de runbook para um Hybrid Runbook Worker

Como eles acessam recursos que não são do Azure, os runbooks em execução em um Hybrid Runbook Worker não podem usar o mecanismo de autenticação normalmente usado pelos runbooks que se autenticam nos recursos do Azure. Um runbook fornece sua própria autenticação aos recursos locais, ou configura a autenticação usando [entidades gerenciadas para os recursos do Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Você também pode especificar uma conta Executar como para fornecer um contexto de usuário a todos os runbooks.

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Para saber como solucionar problemas de seus Hybrid Runbook Workers, veja [Solucionar Problemas do Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).
