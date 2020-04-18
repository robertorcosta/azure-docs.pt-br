---
title: Solução Gerenciamento de Atualizações no Azure
description: Este artigo descreve como usar a solução Azure Update Management para gerenciar atualizações para suas máquinas Windows e Linux.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: 9bdc8cf97513854cf6a92ffd078febca6302d35c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617401"
---
# <a name="update-management-solution-in-azure"></a>Solução Gerenciamento de Atualizações no Azure

Você pode usar a **solução Update Management** no Azure Automation para gerenciar atualizações do sistema operacional para suas máquinas Windows e Linux no Azure, em ambientes locais e em outros ambientes na nuvem. Você pode avaliar rapidamente o status das atualizações disponíveis em todas as máquinas de agentes e gerenciar o processo de instalação de atualizações necessárias para servidores.

Você pode habilitar o Update Management para máquinas virtuais (VMs) usando os seguintes métodos:

- A partir de sua [conta Azure Automation](automation-onboard-solutions-from-automation-account.md) para uma ou mais máquinas Azure e manualmente para máquinas não-Azure.

- Para uma única VM Azure da página da máquina Virtual no portal Azure. Este cenário está disponível para [VMs Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- Para [várias VMs do Azure,](manage-update-multi.md) selecionando-as na página de máquinas virtuais no portal Azure. 

> [!NOTE]
> A **solução Update Management** requer a vinculação de um espaço de trabalho do Log Analytics à sua conta de Automação. Para obter uma lista definitiva de regiões suportadas, consulte [mapeamentos do Azure Workspace](./how-to/region-mappings.md). Os mapeamentos da região não afetam a capacidade de gerenciar VMs em uma região separada da sua conta de Automação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Um modelo do Azure [Resource Manager](automation-update-management-deploy-template.md) está disponível para ajudá-lo a implantar a solução de gerenciamento **de atualizações** em uma conta de automação nova ou existente e espaço de trabalho do Log Analytics em sua assinatura.

## <a name="solution-overview"></a>Visão geral da solução

As máquinas gerenciadas pelo Update Management usam as seguintes configurações para realizar a avaliação e atualizar implantações:

* Agente de Análise de Log para Windows ou Linux
* DSC (PowerShell Desired State Configuration) para Linux
* Hybrid Runbook Worker de Automação
* Serviços de atualização do Microsoft ou Windows Server (WSUS) para máquinas Windows

O diagrama a seguir ilustra como a solução avalia e aplica atualizações de segurança a todas as máquinas Conectadas do Windows Server e Linux em um espaço de trabalho:

![Fluxo do processo de Gerenciamento de Atualizações](./media/automation-update-management/update-mgmt-updateworkflow.png)

O Gerenciamento de Atualizações pode ser usado para integrar nativamente computadores em várias assinaturas no mesmo locatário.

Depois que um pacote é lançado, leva de 2 a 3 horas para o patch aparecer para máquinas Linux para avaliação. Para máquinas Windows, leva de 12 a 15 horas para o patch aparecer para avaliação depois de lançado.

Depois que uma máquina completa uma varredura para conformidade de atualização, o agente encaminha as informações em massa para os registros do Monitor do Azure. Em uma máquina Windows, a varredura de conformidade é executada a cada 12 horas por padrão.

Além do cronograma de varredura, a varredura para conformidade de atualização é iniciada dentro de 15 minutos após o agente log analytics ser reiniciado, antes da instalação da atualização e após a instalação da atualização.

Para uma máquina Linux, a varredura de conformidade é realizada a cada hora por padrão. Se o agente log analytics for reiniciado, uma varredura de conformidade será iniciada dentro de 15 minutos.

A solução relata como a máquina está atualizada com base na fonte com a qual você está configurado para sincronizar. Se a máquina do Windows estiver configurada para reportar ao WSUS, dependendo de quando o WSUS foi sincronizado pela última vez com o Microsoft Update, os resultados podem diferir do que o Microsoft Update mostra. Esse comportamento é o mesmo para máquinas Linux que são configuradas para reportar a um repo local em vez de a um repo público.

> [!NOTE]
> Para relatar adequadamente o serviço, o Gerenciamento de Atualizações requer que determinadas URLs e portas sejam habilitadas. Para saber mais sobre esses requisitos, consulte [Planejamento de rede para Hybrid Workers](automation-hybrid-runbook-worker.md#network-planning).

Você pode implantar e instalar atualizações de software em máquinas que requerem as atualizações criando uma implantação programada. As atualizações classificadas como *opcionais* não estão incluídas no escopo de implantação das máquinas Windows. Somente as atualizações necessárias são incluídas no escopo de implantação.

A implantação programada define quais máquinas-alvo recebem as atualizações aplicáveis. Ele faz isso especificando explicitamente certas máquinas ou selecionando um grupo de [computador](../azure-monitor/platform/computer-groups.md) baseado em pesquisas de log de um conjunto específico de máquinas (ou em uma [consulta do Azure](automation-update-management-query-logs.md) que seleciona dinamicamente As VMs do Azure com base em critérios especificados). Esses grupos diferem da configuração de [escopo,](../azure-monitor/insights/solution-targeting.md)que é usada apenas para determinar quais máquinas recebem os pacotes de gerenciamento que habilitam a solução.

Você também especifica um cronograma para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Esse período é chamado de janela de manutenção. Um período de 20 minutos da janela de manutenção é reservado para reinicializações, supondo que uma seja necessária e você selecionou a opção de reinicialização apropriada. Se o patch for mais longo do que o esperado e houver menos de 20 minutos na janela de manutenção, uma reinicialização não ocorrerá.

As atualizações são instaladas por runbooks na Automação do Azure. Você não pode ver esses runbooks, e eles não exigem nenhuma configuração. Quando uma implantação de atualização é criada, ela cria um cronograma que inicia um manual de atualização mestre no horário especificado para as máquinas incluídas. O runbook mestre inicia um runbook filho em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implantação da atualização, as máquinas de destino executam a implantação em paralelo. Antes da instalação, uma verificação é executada para verificar se as atualizações ainda são necessárias. Para as máquinas clientes WSUS, se as atualizações não forem aprovadas no WSUS, a implantação de atualizações falhará.

Não é suportado ter uma máquina registrada para o Gerenciamento de Atualizações em mais de um espaço de trabalho do Log Analytics (também chamado de multihoming).

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir lista os sistemas operacionais suportados para avaliações de atualização. O patch requer um Trabalhador híbrido do runbook. Para obter informações sobre os requisitos do Hybrid Runbook Worker, consulte as guias de instalação para instalar um [Trabalhador de Runbook Híbrido do Windows](automation-windows-hrw-install.md) e um Trabalhador de [Runbook Híbrido Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (PADRÃO RTM e SP1)| O Gerenciamento de Atualizações só suporta a realização de avaliações para este sistema operacional, o patch não é suportado, pois o [Hybrid Runbook Worker](automation-windows-hrw-install.md) não é suportado para o Windows Server 2008 R2. |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes Linux exigem acesso a um repositório de atualização. O patch baseado `yum` em classificação requer o retorno de dados de segurança que o CentOS não tem em suas versões RTM. Para obter mais informações sobre patches baseados em classificação no CentOS, consulte [Classificações de atualização no Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes Linux exigem acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes Linux exigem acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS, e 18.04 (x86/x64)      |Os agentes Linux exigem acesso a um repositório de atualização.         |

> [!NOTE]
> Os conjuntos de escala de máquinas virtuais do Azure podem ser gerenciados através do Update Management. O Gerenciamento de atualização funciona nas instâncias em si e não na imagem base. Você precisará agendar as atualizações de forma incremental, para que nem todas as instâncias de VM sejam atualizadas de uma só vez.
> Você pode adicionar nós para conjuntos de escala de máquinavirtual seguindo as etapas em [Onboard a uma máquina não-Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

A tabela a seguir lista sistemas operacionais sem suporte:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Client     | Os sistemas operacionais clientes (como Windows 7 e Windows 10) não têm suporte.        |
|Windows Server 2016 Nano Server     | Sem suporte.       |
|Azure Kubernetes Service Nodes | Sem suporte. Use o processo de patches descrito em Aplicar atualizações de [segurança e kernel aos nós Linux no Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisitos do cliente

As informações a seguir descrevem os requisitos específicos do cliente do SISTEMA OPERACIONAL. Para obter orientações adicionais, consulte [o planejamento da rede](#ports).

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para se comunicar com um servidor WSUS, ou eles requerem acesso ao Microsoft Update.

Você pode usar o Gerenciamento de Atualizações com o Gerenciador de Configuração. Para saber mais sobre cenários de integração, consulte [Integrar Gerenciador de Configuração com Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md#configuration). O [agente do Log Analytics Windows](../azure-monitor/platform/agent-windows.md) é necessário. O agente é instalado automaticamente se você estiver embarcando em uma VM Azure.

Por padrão, as VMs do Windows que são implantadas no Azure Marketplace estão definidas para receber atualizações automáticas do Windows Update Service. Esse comportamento não muda quando você adiciona essa solução ou adiciona VMs do Windows ao seu espaço de trabalho. Se você não gerenciou atualizações ativamente usando essa solução, o comportamento padrão (para aplicar automaticamente as atualizações) é aplicado.

> [!NOTE]
> Um usuário pode modificar a Política de Grupo para que as reinicializações da máquina possam ser realizadas apenas pelo usuário, não pelo sistema. As máquinas gerenciadas podem ficar presas se o Gerenciamento de Atualizações não tiver os direitos de reiniciar a máquina sem a interação manual do usuário.
>
> Para obter mais informações, consulte [Configurar configurações de diretiva de grupo para atualizações automáticas](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Para linux, a máquina requer acesso a um repositório de atualização. O repositório de atualização pode ser público ou privado. TLS 1.1 ou TLS 1.2 é necessário para interagir com o gerenciamento de atualizações. Um Agente de Análise de Log para Linux configurado para reportar a mais de um espaço de trabalho do Log Analytics não é suportado com essa solução. A máquina também deve ter python 2.x instalado.

Para obter informações sobre como instalar o agente Log Analytics para Linux e baixar a versão mais recente, consulte [o agente Log Analytics para Linux](../azure-monitor/platform/agent-linux.md). Para obter informações sobre como instalar o agente Log Analytics para Windows, consulte [Conecte computadores Windows ao Monitor Do Azure](../log-analytics/log-analytics-windows-agent.md).

As VMs que foram criadas a partir das imagens RHEL (Red Hat Enterprise Linux) sob demanda que estão disponíveis no Azure Marketplace estão registradas para acessar a [Red Hat Update Infrastructure (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) implantada no Azure. Qualquer outra distribuição Linux deve ser atualizada do repositório de arquivos on-line da distribuição usando os métodos suportados pela distribuição.

## <a name="permissions"></a>Permissões

Para criar e gerenciar implantações de atualização, você precisa ter permissões específicas. Para saber mais sobre essas permissões, consulte [acesso baseado em função – Gerenciamento de atualizações](automation-role-based-access-control.md#update-management-permissions).

## <a name="solution-components"></a>Componentes da solução

A solução consiste nos recursos a seguir. Esses recursos são adicionados automaticamente à sua conta de Automação quando você habilita a solução. 

### <a name="hybrid-worker-groups"></a>Grupos de Hybrid Worker

Depois de habilitar essa solução, qualquer máquina do Windows conectada diretamente ao seu espaço de trabalho do Log Analytics é configurada automaticamente como um Trabalhador de Runbook Híbrido para suportar os runbooks incluídos nesta solução.

Cada máquina Windows gerenciada pela solução é listada no painel de grupos de trabalhadores híbridos como um grupo de trabalhadores híbridos do sistema para a conta de Automação. As soluções `Hostname FQDN_GUID` usam a convenção de nomeação. Não é possível direcionar esses grupos com runbooks em sua conta. Se você tentar, a tentativa falha. Esses grupos destinam-se a apoiar apenas essa solução de gestão.

Você pode adicionar a máquina do Windows a um grupo hybrid Runbook Worker em sua conta de automação para suportar livros de execução de automação se você usar a mesma conta tanto para a solução quanto para a associação do grupo Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="management-packs"></a>Pacotes de gerenciamento

Se o grupo de gerenciamento do System Center Operations Manager estiver [conectado a um espaço de trabalho do Log Analytics,](../azure-monitor/platform/om-agents.md)os seguintes pacotes de gerenciamento serão instalados no Gerenciador de Operações. Esses pacotes de gerenciamento também são instalados em máquinas Windows conectadas diretamente depois de adicionar a solução. Você não precisa configurar ou gerenciar esses pacotes de gerenciamento.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP de Implantação de Atualizações

> [!NOTE]
> Se você tiver um grupo de gerenciamento do Operations Manager 1807 ou 2019 conectado a um espaço de trabalho do `IsAutoRegistrationEnabled` Log Analytics com agentes configurados no grupo de gerenciamento para coletar dados de log, você precisa substituir o parâmetro e defini-lo como True na regra **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init.**

Para obter mais informações sobre como os pacotes de gerenciamento de soluções são atualizados, consulte [Connect Operations Manager nos registros do Monitor Do Azure](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para que as máquinas com o agente Operations Manger sejam totalmente gerenciadas pelo Update Management, o agente deve ser atualizado para o agente log analytics para Windows ou para o agente Log Analytics para Linux. Para saber como atualizar o agente, consulte [como atualizar um agente do Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Em ambientes que usam o Gerente de Operações, você deve estar executando o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="data-collection"></a>Coleta de dados

### <a name="supported-agents"></a>Agentes com suporte

A tabela a seguir descreve as fontes conectadas às quais essa solução dá suporte:

| Fonte conectada | Com suporte | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução coleta informações sobre atualizações do sistema de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |A solução coleta informações sobre atualizações do sistema de agentes para Linux e, em seguida, inicia a instalação das atualizações necessárias nas distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |Sim |A solução coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectados.<br/><br/>Não é necessária uma conexão direta do agente do Gerente de Operações com os registros do Monitor Do Azure. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência de coleta

Uma varredura é realizada duas vezes por dia para cada máquina do Windows gerenciada. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status foi alterado. Se o status foi alterado, uma verificação de conformidade é iniciada.

Uma varredura é realizada a cada hora para cada máquina Linux gerenciada.

Pode levar entre 30 minutos e 6 horas para o painel exibir dados atualizados de máquinas gerenciadas.

O uso médio de dados pelos logs do Azure Monitor para uma máquina usando o Update Management é de aproximadamente 25 megabytes (MB) por mês. Este valor é apenas uma aproximação e está sujeito a alterações, dependendo do seu ambiente. Recomendamos que você monitore seu ambiente para acompanhar seu uso exato. Para obter mais informações para analisar o uso de dados, consulte [Gerenciar o uso e o custo](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planejamento de rede

Os endereços a seguir são necessários especificamente para gerenciamento de atualizações. A comunicação para esses endereços ocorre pela porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.Azure automation.us|

Para máquinas Windows, você também deve permitir o tráfego para quaisquer pontos finais exigidos pelo Windows Update. Você pode encontrar uma lista atualizada de pontos finais necessários em [Problemas relacionados ao HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se você tiver um [servidor local do Windows Update,](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)você também deve permitir o tráfego para o servidor especificado na chave [WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Para máquinas Red Hat Linux, consulte [IPs para os servidores de entrega de conteúdo RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) para pontos finais necessários. Para outras distribuições Linux, consulte a documentação do provedor.

Para obter mais informações sobre as portas necessárias para o Híbrido Runbook Worker, consulte [Portas de função Hybrid Worker](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Recomendamos que você use os endereços listados ao definir exceções. Para endereços IP, você pode baixar as [faixas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este arquivo é atualizado semanalmente e reflete as faixas atualmente implantadas e quaisquer alterações próximas nas faixas IP.

Siga as instruções em [Conectar computadores sem acesso](../azure-monitor/platform/gateway.md) à internet para configurar máquinas que não têm acesso à internet.

## <a name="update-classifications"></a>Classificações de origem

As tabelas a seguir listam as classificações de atualização no Gerenciamento de Atualizações, com uma definição de cada classificação.

### <a name="windows"></a>Windows

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que aborda um bug crítico não relacionado à segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico do produto relacionadas à segurança.        |
|Pacotes cumulativos de atualização     | Um conjunto cumulativo de hotfixes que são reunidos para facilitar a implantação.        |
|Feature packs     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definição     | Uma atualização para vírus ou outros arquivos de definição.        |
|Ferramentas     | Um utilitário ou recurso que ajuda a concluir uma ou mais tarefas.        |
|Atualizações     | Uma atualização para um aplicativo ou arquivo que está atualmente instalado.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas na natureza ou que não são atualizações de segurança.        |

Para o Linux, o Update Management pode distinguir entre atualizações críticas e atualizações de segurança na nuvem enquanto exibe dados de avaliação devido ao enriquecimento de dados na nuvem. Para aplicação de patch, o Gerenciamento de Atualizações se baseia em dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis na versão RTM. Se você tiver as máquinas CentOS configuradas para retornar dados de segurança para o seguinte comando, o Update Management pode corrigir com base em classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não há nenhum método suportado para permitir a disponibilidade nativa de dados de classificação no CentOS. Neste momento, apenas o suporte de melhor esforço é fornecido aos clientes que podem ter habilitado isso por conta própria. 

Para classificar as atualizações na versão 6 do Red Hat Enterprise, você precisa instalar o plugin yum-security. No Red Hat Enterprise Linux 7, o plugin já faz parte do próprio yum, não há necessidade de instalar nada. Para obter mais informações, consulte o [seguinte artigo de conhecimento](https://access.redhat.com/solutions/10021)da Red Hat .

## <a name="integrate-with-configuration-manager"></a>Integrar com o Configuration Manager

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também contam com a força e a maturidade do Gerenciador de Configuração para ajudá-los a gerenciar atualizações de software. Configuration Manager é parte de seu ciclo de gerenciamento de atualizações de software (SUM).

Para saber como integrar a solução de gerenciamento com o Gerenciador de Configuração, consulte [Integrar O Gerenciador de Configuração com o Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-updates-on-windows"></a>Atualizações de terceiros no Windows

O Gerenciamento de atualizações conta com o repositório de atualização configurado localmente para atualizar sistemas Windows suportados. Isto é WSUS ou Windows Update. Ferramentas como [system center updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher) permite importar e publicar atualizações personalizadas com o WSUS. Este cenário permite que o Update Management atualize máquinas que usam o Configuration Manager como seu repositório de atualização com software de terceiros. Para saber como configurar o Updates Publisher, veja [Instalar o Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a><a name="onboard"></a>Habilitar o gerenciamento de atualizações

Para começar a atualizar sistemas, você precisa ativar a solução Update Management. A seguir estão os métodos recomendados e suportados para embarcar na solução:

- [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
- [Da navegação em várias máquinas](automation-onboard-solutions-from-browse.md)
- [Da sua conta de Automação](automation-onboard-solutions-from-automation-account.md)
- [Com um runbook de Automação do Azure](automation-onboard-solutions.md)
- [Com um modelo de Gerenciador de recursos do Azure](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>Próximas etapas

Revise a [FAQ de](automation-faq.md) Automação do Azure para revisar perguntas comuns sobre essa solução.
