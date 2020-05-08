---
title: Gerenciamento de Atualizações na automação do Azure
description: Este artigo descreve o Gerenciamento de Atualizações recurso que gerencia atualizações para seus computadores Windows e Linux.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: ca5d8c35aea06143e058aade473282a038212605
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872158"
---
# <a name="update-management-in-azure-automation"></a>Gerenciamento de Atualizações na automação do Azure

Você pode usar Gerenciamento de Atualizações na automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux no Azure, em ambientes locais e em outros ambientes de nuvem. Você pode avaliar rapidamente o status das atualizações disponíveis em todas as máquinas do agente e gerenciar o processo de instalação das atualizações necessárias para os servidores.

Você pode habilitar Gerenciamento de Atualizações para VMs (máquinas virtuais) usando os seguintes métodos:

* Da sua [conta de automação do Azure](automation-onboard-solutions-from-automation-account.md) para um ou mais computadores do Azure.
* Manualmente para computadores não Azure.
* Para uma única VM do Azure da página da máquina virtual no portal do Azure. Esse cenário está disponível para VMs [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .
* Para [várias VMs do Azure](manage-update-multi.md) , selecione-as na página máquinas virtuais no portal do Azure.

> [!NOTE]
> Gerenciamento de Atualizações requer a vinculação de um espaço de trabalho Log Analytics à sua conta de automação. Para obter uma lista definitiva de regiões com suporte, consulte [mapeamentos de espaço de trabalho do Azure](how-to/region-mappings.md). Os mapeamentos de região não afetam a capacidade de gerenciar VMs em uma região separada da sua conta de automação.

Um [modelo de Azure Resource Manager](automation-update-management-deploy-template.md) está disponível para ajudá-lo a implantar gerenciamento de atualizações em uma conta de automação nova ou existente e log Analytics espaço de trabalho em sua assinatura.

> [!NOTE]
> Você não pode usar um computador configurado com Gerenciamento de Atualizações para executar scripts personalizados da automação do Azure. Este computador só pode executar o script de atualização assinado pela Microsoft. 

## <a name="update-management-overview"></a>Visão geral do Gerenciamento de Atualizações

As máquinas gerenciadas pelo Gerenciamento de Atualizações usam as seguintes configurações para executar a avaliação e para atualizar as implantações:

* Agente de Log Analytics para Windows ou Linux
* DSC (PowerShell Desired State Configuration) para Linux
* Hybrid Runbook Worker de Automação
* Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

O diagrama a seguir ilustra como o Gerenciamento de Atualizações avalia e aplica atualizações de segurança a todos os computadores Windows Server e Linux conectados em um espaço de trabalho:

![Fluxo de trabalho Gerenciamento de Atualizações](./media/automation-update-management/update-mgmt-updateworkflow.png)

O Gerenciamento de Atualizações pode ser usado para integrar nativamente computadores em várias assinaturas no mesmo locatário.

Depois que um pacote é liberado, leva de 2 a 3 horas para que o patch seja exibido para as máquinas Linux para avaliação. Para computadores Windows, leva de 12 a 15 horas para que o patch seja mostrado para avaliação após sua liberação.

Depois que um computador concluir uma verificação de conformidade de atualização, o agente encaminhará as informações em massa para Azure Monitor logs. Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão.

Além do agendamento de verificação, a verificação de conformidade de atualização é iniciada em 15 minutos do agente de Log Analytics que está sendo reiniciado, antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente de Log Analytics for reiniciado, uma verificação de conformidade será iniciada dentro de 15 minutos.

Gerenciamento de Atualizações relata o quão atualizado o computador é baseado em qual fonte você está configurado para sincronizar. Se o computador Windows estiver configurado para relatar para o WSUS, dependendo de quando o WSUS foi sincronizado pela última vez com Microsoft Update, os resultados poderão ser diferentes do que Microsoft Update mostra. Esse comportamento é o mesmo para computadores Linux configurados para relatar a um repositório local, em vez de a um repositório público.

> [!NOTE]
> Para relatar adequadamente o serviço, o Gerenciamento de Atualizações requer que determinadas URLs e portas sejam habilitadas. Para saber mais sobre esses requisitos, consulte [configuração de rede](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#network-planning).

Você pode implantar e instalar atualizações de software em computadores que exigem as atualizações, criando uma implantação agendada. As atualizações classificadas como opcionais não são incluídas no escopo de implantação para computadores Windows. Somente as atualizações necessárias são incluídas no escopo de implantação.

A implantação agendada define quais computadores de destino recebem as atualizações aplicáveis. Ele faz isso especificando explicitamente determinados computadores ou selecionando um grupo de [computadores](https://docs.microsoft.com/azure/azure-monitor/platform/computer-groups) baseado em pesquisas de log de um conjunto específico de computadores (ou em uma [consulta do Azure](automation-update-management-query-logs.md) que seleciona dinamicamente VMs do Azure com base em critérios especificados). Esses grupos diferem da [configuração de escopo](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting), que é usada para controlar o direcionamento de computadores que recebem a configuração para habilitar o gerenciamento de atualizações. Isso impede que eles executem e relatem a conformidade da atualização e instalem atualizações necessárias aprovadas.

Ao definir uma implantação, você também especifica uma agenda para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Esse período é chamado de janela de manutenção. Um período de 20 minutos da janela de manutenção é reservado para reinicializações, supondo que uma seja necessária e você tenha selecionado a opção de reinicialização apropriada. Se a aplicação de patch demorar mais do que o esperado e houver menos de 20 minutos na janela de manutenção, uma reinicialização não ocorrerá.

As atualizações são instaladas por runbooks na Automação do Azure. Você não pode exibir esses runbooks e eles não exigem nenhuma configuração. Quando uma implantação de atualização é criada, ela cria uma agenda que inicia um runbook de atualização mestre no momento especificado para os computadores incluídos. O runbook mestre inicia um runbook filho em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implantação da atualização, os computadores de destino executam a implantação em paralelo. Antes da instalação, uma verificação é executada para verificar se as atualizações ainda são necessárias. Para computadores cliente do WSUS, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará.
Ter um computador registrado para Gerenciamento de Atualizações em mais de um espaço de trabalho Log Analytics (também conhecido como hospedagem múltipla) não tem suporte.

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir lista os sistemas operacionais com suporte para avaliações de atualização. A aplicação de patch requer um Hybrid Runbook Worker. Para obter informações sobre os requisitos de Hybrid Runbook Worker, consulte [implantar um Hybrid runbook Worker do Windows](automation-windows-hrw-install.md) e um [Hybrid runbook Worker implantar um Linux](automation-linux-hrw-install.md).

> [!NOTE]
> A avaliação de atualização de computadores Linux só tem suporte em determinadas regiões, conforme listado na conta de automação e Log Analytics [tabela de mapeamentos](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)de espaço de trabalho. 

|Sistema operacional  |Anotações  |
|---------|---------|
|Windows Server 2019 (datacenter/Data Center Core/Standard)<br><br>Windows Server 2016 (datacenter/Data Center Core/Standard)<br><br>Windows Server 2012 R2 (datacenter/padrão)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM e SP1 Standard)| Gerenciamento de Atualizações só dá suporte a avaliações para este sistema operacional. Não há suporte para aplicação de patches, pois não há suporte para o [Hybrid runbook Worker](automation-windows-hrw-install.md) no Windows Server 2008 R2. |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux exigem acesso a um repositório de atualização. A aplicação de patch baseada em `yum` classificação requer que o retorne dados de segurança que o CentOS não tem em suas versões RTM. Para obter mais informações sobre aplicação de patch com base em classificação no CentOS, consulte [Atualizar classificações no Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux exigem acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux exigem acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS, e 18.04 (x86/x64)      |Os agentes do Linux exigem acesso a um repositório de atualização.         |

> [!NOTE]
> Os conjuntos de dimensionamento de máquinas virtuais do Azure podem ser gerenciados por meio de Gerenciamento de Atualizações. Gerenciamento de Atualizações funciona nas instâncias e não na imagem base. Você precisará agendar as atualizações de forma incremental, para que nem todas as instâncias de VM sejam atualizadas ao mesmo tempo.
> Você pode adicionar nós para conjuntos de dimensionamento de máquinas virtuais seguindo as etapas em [carregar um computador não Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

A tabela a seguir lista os sistemas operacionais sem suporte:

|Sistema operacional  |Anotações  |
|---------|---------|
|Windows Client     | Os sistemas operacionais clientes (como Windows 7 e Windows 10) não têm suporte.        |
|Windows Server 2016 Nano Server     | Não há suporte.       |
|Nós do serviço kubernetes do Azure | Não há suporte. Use o processo de aplicação de patch descrito em [aplicar segurança e atualizações de kernel a nós do Linux no serviço kubernetes do Azure (AKs)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisitos do cliente

As informações a seguir descrevem os requisitos de cliente específicos do sistema operacional. Para obter diretrizes adicionais, consulte [planejamento de rede](#ports).

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para se comunicar com um servidor WSUS ou exigem acesso a Microsoft Update. Para obter informações sobre como instalar o agente de Log Analytics para Windows, consulte [conectar computadores Windows ao Azure monitor](../log-analytics/log-analytics-windows-agent.md).

Você pode usar Gerenciamento de Atualizações com o Microsoft Endpoint Configuration Manager. Para saber mais sobre cenários de integração, consulte [integrar Configuration Manager com gerenciamento de atualizações](updatemgmt-mecmintegration.md#configuration). O [agente do log Analytics para Windows](../azure-monitor/platform/agent-windows.md) é necessário para servidores Windows gerenciados por sites em seu ambiente de Configuration Manager. 

Por padrão, as VMs do Windows que são implantadas no Azure Marketplace são definidas para receber atualizações automáticas do serviço Windows Update. Esse comportamento não é alterado quando você adiciona VMs do Windows ao seu espaço de trabalho. Se você não gerenciar atualizações ativamente usando Gerenciamento de Atualizações, o comportamento padrão (para aplicar atualizações automaticamente) será aplicado.

> [!NOTE]
> Você pode modificar Política de Grupo para que as reinicializações do computador possam ser executadas somente pelo usuário, não pelo sistema. Os computadores gerenciados podem ficar presos se Gerenciamento de Atualizações não tiver direitos para reinicializar o computador sem interação manual do usuário. Para obter mais informações, consulte [definir configurações de política de grupo para atualizações automáticas](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Para o Linux, o computador requer acesso a um repositório de atualização, seja privado ou público. TLS 1.1 ou TLS 1.2 é necessário para interagir com o gerenciamento de atualizações. Gerenciamento de Atualizações não dá suporte a um agente de Log Analytics para Linux configurado para relatar mais de um espaço de trabalho Log Analytics. O computador também deve ter o Python 2. x instalado.

> [!NOTE]
> A avaliação de atualização de computadores Linux só tem suporte em determinadas regiões. Consulte a [tabela de mapeamentos](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)de conta de automação e de log Analytics espaço de trabalho. 

Para obter informações sobre como instalar o agente de Log Analytics para Linux e baixar a versão mais recente, consulte [log Analytics Agent para Linux](../azure-monitor/platform/agent-linux.md). 

As VMs criadas a partir das imagens de Red Hat Enterprise Linux (RHEL) sob demanda que estão disponíveis no Azure Marketplace são registradas para acessar a [RHUI (infraestrutura de atualização do Red Hat)](../virtual-machines/workloads/redhat/redhat-rhui.md) implantada no Azure. Qualquer outra distribuição do Linux deve ser atualizada no repositório de arquivos online da distribuição usando métodos com suporte da distribuição.

## <a name="permissions"></a>Permissões

Para criar e gerenciar implantações de atualização, você precisa ter permissões específicas. Para saber mais sobre essas permissões, consulte [acesso baseado em função – gerenciamento de atualizações](automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Componentes do Gerenciamento de Atualizações

Gerenciamento de Atualizações usa os recursos descritos nesta seção. Esses recursos são adicionados automaticamente à sua conta de automação quando você habilita Gerenciamento de Atualizações.

### <a name="hybrid-runbook-worker-groups"></a>Grupos de Runbook Worker Híbrido

Depois de habilitar Gerenciamento de Atualizações, qualquer computador Windows conectado diretamente ao seu espaço de trabalho do Log Analytics é automaticamente configurado como um Hybrid Runbook Worker para dar suporte aos runbooks que dão suporte a Gerenciamento de Atualizações.

Cada computador Windows que é gerenciado pelo Gerenciamento de Atualizações está listado no painel grupos do Hybrid Worker como um grupo de trabalho híbrido do sistema para a conta de automação. Os grupos usam a `Hostname FQDN_GUID` Convenção de nomenclatura. Não é possível direcionar esses grupos com runbooks em sua conta. Se você tentar, a tentativa falhará. Esses grupos têm o objetivo de dar suporte apenas a Gerenciamento de Atualizações.

Você pode adicionar o computador Windows a um grupo de Hybrid Runbook Worker em sua conta de automação para dar suporte a runbooks de automação se usar a mesma conta para Gerenciamento de Atualizações e a associação de grupo de Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="management-packs"></a>Pacotes de gerenciamento

Se o grupo de gerenciamento do Operations Manager estiver [conectado a um espaço de trabalho do log Analytics](../azure-monitor/platform/om-agents.md), os pacotes de gerenciamento a seguir serão instalados no Operations Manager. Esses pacotes de gerenciamento também são instalados para Gerenciamento de Atualizações em computadores Windows conectados diretamente. Você não precisa configurar ou gerenciar esses pacotes de gerenciamento.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP de Implantação de Atualizações

> [!NOTE]
> Se você tiver um grupo de gerenciamento Operations Manager 1807 ou 2019 conectado a um espaço de trabalho Log Analytics com agentes configurados no grupo de gerenciamento para coletar dados de log, `IsAutoRegistrationEnabled` será necessário substituir o parâmetro e defini-lo como true na regra **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Para obter mais informações sobre atualizações de pacotes de gerenciamento, consulte [conectar Operations Manager a logs de Azure monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para Gerenciamento de Atualizações gerenciar totalmente as máquinas com o agente de Log Analytics, você deve atualizar para o agente do Log Analytics para Windows ou o agente do Log Analytics para Linux. Para saber como atualizar o agente, consulte [como atualizar um agente do Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Em ambientes que usam Operations Manager, você deve estar executando o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="data-collection"></a>Coleta de dados

### <a name="supported-sources"></a>Fontes com suporte

A tabela a seguir descreve as fontes conectadas às quais Gerenciamento de Atualizações oferece suporte:

| Fonte conectada | Com suporte | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes do Windows e, em seguida, inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes do Linux e, em seguida, inicia a instalação de atualizações necessárias em distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |Sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectado.<br/><br/>Não é necessária uma conexão direta do agente de Operations Manager para os logs do Azure Monitor. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência de coleta

Gerenciamento de Atualizações verifica os dados em computadores gerenciados usando as regras a seguir. Pode levar entre 30 minutos e 6 horas para que o painel exiba dados atualizados de computadores gerenciados.

* Cada computador com Windows-Gerenciamento de Atualizações faz uma verificação duas vezes por dia para cada computador. A cada 15 minutos, ele consulta a API do Windows para a hora da última atualização para determinar se o status foi alterado. Se o status tiver sido alterado, Gerenciamento de Atualizações iniciará uma verificação de conformidade.

* Cada computador Linux – Gerenciamento de Atualizações faz uma verificação a cada hora.

O uso médio de dados por Azure Monitor logs para um computador usando Gerenciamento de Atualizações é de aproximadamente 25 MB por mês. Esse valor é apenas uma aproximação e está sujeito a alterações, dependendo do seu ambiente. Recomendamos que você monitore seu ambiente para controlar seu uso exato. Para obter mais informações para analisar o uso de dados, consulte [gerenciar o uso e o custo](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planejamento de rede

Os endereços a seguir são necessários especificamente para gerenciamento de atualizações. A comunicação para esses endereços ocorre pela porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.Azure automation.us|

Para computadores Windows, você também deve permitir o tráfego para qualquer ponto de extremidade exigido pelo Windows Update. Você pode encontrar uma lista atualizada de pontos de extremidade necessários em [problemas relacionados ao http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se você tiver um [servidor de Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)local, também deverá permitir o tráfego para o servidor especificado em sua [chave do WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Para computadores Red Hat Linux, consulte [IPS para os servidores de distribuição de conteúdo RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) para pontos de extremidade necessários. Para outras distribuições do Linux, consulte a documentação do provedor.

Para obter mais informações sobre as portas necessárias para o Hybrid Runbook Worker, consulte [Gerenciamento de atualizações endereços para Hybrid runbook Worker](automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Recomendamos que você use os endereços listados ao definir exceções. Para endereços IP, você pode baixar [Microsoft Azure intervalos de IP do datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e reflete os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Siga as instruções em [conectar computadores sem acesso à Internet](../azure-monitor/platform/gateway.md) para configurar computadores que não têm acesso à Internet.

## <a name="update-classifications"></a>Classificações de origem

A tabela a seguir define as classificações que Gerenciamento de Atualizações oferece suporte para atualizações do Windows. 

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

A tabela a seguir define as classificações com suporte para atualizações do Linux.

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas por natureza ou que não são atualizações de segurança.        |

Para o Linux, Gerenciamento de Atualizações pode distinguir entre atualizações críticas e atualizações de segurança na nuvem enquanto exibe dados de avaliação devido ao enriquecimento de dados na nuvem. Para aplicação de patch, o Gerenciamento de Atualizações se baseia em dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis na versão RTM. Se você tiver máquinas CentOS configuradas para retornar dados de segurança para o comando a seguir, Gerenciamento de Atualizações poderá aplicar patches com base nas classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não há um método com suporte para habilitar a disponibilidade de dados de classificação nativa no CentOS. Neste momento, apenas o suporte de melhor esforço é fornecido aos clientes que podem ter habilitado esse recurso por conta própria. 

Para classificar atualizações no Red Hat Enterprise versão 6, você precisa instalar o plug-in yum-Security. No Red Hat Enterprise Linux 7, o plug-in já faz parte do próprio yum e não há necessidade de instalar nada. Para obter mais informações, consulte o seguinte [artigo de conhecimento](https://access.redhat.com/solutions/10021)do Red Hat.

## <a name="integrate-update-management-with-configuration-manager"></a>Integrar Gerenciamento de Atualizações com Configuration Manager

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e da maturidade de Configuration Manager para ajudar a gerenciar atualizações de software. Para saber como integrar o Gerenciamento de Atualizações com o Configuration Manager, consulte [integrar Configuration Manager com gerenciamento de atualizações](updatemgmt-mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Atualizações de terceiros no Windows

Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para atualizar os sistemas Windows com suporte, ou seja, WSUS ou Windows Update. Ferramentas como [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) permitem que você importe e publique atualizações personalizadas com o WSUS. Esse cenário permite que Gerenciamento de Atualizações atualize as máquinas que usam Configuration Manager como seu repositório de atualizações com software de terceiros. Para saber como configurar o Updates Publisher, veja [Instalar o Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Um modelo do Azure [Resource Manager](automation-update-management-deploy-template.md) está disponível para ajudá-lo a implantar gerenciamento de atualizações em uma conta de automação nova ou existente e Azure monitor espaço de trabalho log Analytics em sua assinatura. Ele não configura o escopo de computadores que devem ser gerenciados, isso é executado como uma etapa separada após o uso do modelo.

Aqui estão as maneiras como você pode habilitar Gerenciamento de Atualizações e selecionar os computadores a serem gerenciados:

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md).
* [De navegar por vários computadores](automation-onboard-solutions-from-browse.md).
* [De uma conta de automação do Azure](automation-onboard-solutions.md).

## <a name="next-steps"></a>Próximas etapas

Examine as [perguntas frequentes](automation-faq.md) sobre a automação do Azure para revisar perguntas comuns sobre gerenciamento de atualizações.
