---
title: Solução Gerenciamento de Atualizações no Azure
description: Este artigo destina-se a ajudá-lo a entender como usar a solução de Gerenciamento de Atualizações do Azure para gerenciar atualizações para os computadores Windows e Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1751e8d67f59285d011df33a2d4d1d6d8abcec6a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376048"
---
# <a name="update-management-solution-in-azure"></a>Solução Gerenciamento de Atualizações no Azure

Você pode usar a solução Gerenciamento de Atualizações na automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux no Azure, em ambientes locais ou em outros provedores de nuvem. Você pode avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar rapidamente o processo de instalação das atualizações necessárias para os servidores.

Você pode habilitar o Gerenciamento de Atualizações para máquinas virtuais diretamente da sua conta da Automação do Azure. Para saber como habilitar o Gerenciamento de Atualizações para máquinas virtuais de sua conta de automação, veja [Gerenciar atualizações para várias máquinas virtuais](manage-update-multi.md). Você também pode habilitar o Gerenciamento de Atualizações para uma máquina virtual da página de máquina virtual no portal do Azure. Esse cenário está disponível para máquinas virtuais do [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management).

> [!NOTE]
> A solução Gerenciamento de Atualizações requer a vinculação de um espaço de trabalho Log Analytics à sua conta de automação. Para obter uma lista definitiva de regiões com suporte, consulte [mapeamentos de espaço de trabalho do Azure](./how-to/region-mappings.md). Os mapeamentos de região não afetam a capacidade de gerenciar máquinas virtuais em uma região separada do que a sua conta de automação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Visão geral da solução

Os computadores que são gerenciados pelo Gerenciamento de Atualizações usam as configurações a seguir para realizar implantações de atualização e avaliação:

* Microsoft Monitoring Agent (MMA) para Windows ou Linux
* DSC (PowerShell Desired State Configuration) para Linux
* Hybrid Runbook Worker de Automação
* Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

O diagrama a seguir mostra uma exibição conceitual do comportamento e do fluxo de dados, indicando como a solução avalia e aplica atualizações de segurança a todos os computadores Linux e servidores Windows conectados em um workspace:

![Fluxo do processo de Gerenciamento de Atualizações](./media/automation-update-management/update-mgmt-updateworkflow.png)

O Gerenciamento de Atualizações pode ser usado para integrar nativamente computadores em várias assinaturas no mesmo locatário.

Depois que um pacote é liberado, leva 2-3 horas para que o patch seja exibido para os computadores Linux para avaliação. Para máquinas Windows, leva de 12 a 15 horas para o patch ser exibido para avaliação após sua liberação.

Depois que um computador conclui uma verificação de conformidade de atualização, o agente encaminha as informações em massa para Azure Monitor logs. Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão.

Além do agendamento da verificação, a verificação de conformidade de atualizações será iniciada em 15 minutos se o MMA estiver sendo reiniciado antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente MMA for reiniciado, uma verificação de conformidade é iniciada dentro de 15 minutos.

A solução relata o grau de atualização do computador com base na fonte com a qual você está configurado para realizar a sincronização. Se o computador do Windows estiver configurado para relatar para o WSUS, dependendo de quando o WSUS foi sincronizado pela última vez com o Microsoft Update, os resultados poderão diferir do que é mostrado pelo Microsoft Updates. Esse comportamento é o mesmo para computadores Linux configurados para relatar a um repositório local em vez de para um repositório público.

> [!NOTE]
> Para relatar adequadamente o serviço, o Gerenciamento de Atualizações requer que determinadas URLs e portas sejam habilitadas. Para saber mais sobre esses requisitos, consulte [Planejamento de rede para Hybrid Workers](automation-hybrid-runbook-worker.md#network-planning).

Você pode implantar e instalar atualizações de software em computadores que precisam de atualizações, criando uma implantação agendada. As atualizações classificadas como *Opcional* não são incluídas no escopo de implantação para computadores Windows. Somente as atualizações necessárias são incluídas no escopo de implantação.

A implantação agendada define quais computadores de destino recebem as atualizações aplicáveis, especificando explicitamente computadores ou selecionando um [grupo](../azure-monitor/platform/computer-groups.md) de computadores baseado em pesquisas de log de um conjunto específico de computadores ou uma [consulta do Azure](automation-update-management-query-logs.md) Isso seleciona dinamicamente as VMs do Azure com base em critérios especificados. Esses grupos são diferentes da [configuração de escopo](../azure-monitor/insights/solution-targeting.md), que é usada apenas para determinar quais computadores obtêm os pacotes de gerenciamento que habilitam a solução.

Você também pode especificar uma agenda para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Esse período de tempo é chamado de janela de manutenção. Vinte minutos da janela de manutenção é reservado para reinicializações se uma reinicialização for necessária e você tiver selecionado a opção de reinicialização apropriada. Se a aplicação de patch demorar mais do que o esperado e houver menos de vinte minutos na janela de manutenção, uma reinicialização não ocorrerá.

As atualizações são instaladas por runbooks na Automação do Azure. Você não consegue exibir esses runbooks e os runbooks não exigem nenhuma configuração. Quando uma implantação de atualizações é criada, a implantação de atualizações cria uma agenda que inicia um runbook de atualização mestre no momento especificado para os computadores incluídos. O runbook mestre inicia um runbook filho em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implantação da atualização, os computadores de destino executam a implantação em paralelo. Antes da instalação, uma verificação é executada para verificar se as atualizações ainda são necessárias. Para computadores cliente WSUS, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará.

Não há suporte para um computador registrado para Gerenciamento de Atualizações em mais de um espaço de trabalho Log Analytics (hospedagem múltipla).

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte para avaliações de atualização. A aplicação de patch requer um Hybrid Runbook Worker. Para obter informações sobre requisitos de Hybrid Runbook Worker, consulte os guias de instalação do [Windows HRW](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) e [Linux HRW](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Sistema operacional  |Notas  |
|---------|---------|
|Windows Server 2019 (datacenter/Data Center Core/Standard)<br><br>Windows Server 2016 (datacenter/Data Center Core/Standard)<br><br>Windows Server 2012 R2 (datacenter/padrão)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM e SP1 Standard)||
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização. O patch baseado em classificação requer que o yum retorne dados de segurança que o CentOS não possui. Para obter mais informações sobre aplicação de patch com base em classificação no CentOS, consulte [Atualizar classificações no Linux](#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS, e 18.04 (x86/x64)      |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

> [!NOTE]
> Os conjuntos de dimensionamento de máquinas virtuais do Azure podem ser gerenciados com Gerenciamento de Atualizações. Gerenciamento de Atualizações funciona nas próprias instâncias e não na imagem base. Você precisará agendar as atualizações de forma incremental, como não atualizar todas as instâncias de VM ao mesmo tempo.
> Nós VMSS podem ser adicionados seguindo as etapas em [carregar um computador não Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

A tabela a seguir lista os sistemas operacionais que não têm suporte:

|Sistema operacional  |Notas  |
|---------|---------|
|Windows Client     | Os sistemas operacionais clientes (como Windows 7 e Windows 10) não têm suporte.        |
|Windows Server 2016 Nano Server     | Sem suporte.       |
|Nós do serviço kubernetes do Azure | Sem suporte. Use o processo de aplicação de patch detalhado em [aplicar segurança e atualizações de kernel a nós do Linux no serviço kubernetes do Azure (AKs)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisitos do cliente

As informações a seguir descrevem os requisitos de cliente específicos do sistema operacional.  Você também deve examinar o [planejamento de rede](#ports) para obter mais diretrizes.

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para comunicarem-se com um servidor WSUS ou eles devem ter acesso ao Microsoft Update.

Você pode usar o Gerenciamento de Atualizações com o System Center Configuration Manager. Para saber mais sobre cenários de integração, consulte [Integrar System Center Configuration Manager com Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md#configuration). O [agente do Windows](../azure-monitor/platform/agent-windows.md) é obrigatório. O agente será instalado automaticamente se você estiver instalando uma máquina virtual do Azure.

Máquinas virtuais do Windows que são implantadas no Azure Marketplace por padrão são definidas para receber atualizações automáticas do Serviço Windows Update. Esse comportamento não é alterado quando você adiciona essa solução ou adiciona máquinas virtuais do Windows para seu workspace. Se você não gerenciou atualizações ativamente usando essa solução, o comportamento padrão (para aplicar automaticamente as atualizações) é aplicado.

> [!NOTE]
> É possível que um usuário modifique Política de Grupo para que as reinicializações do computador só possam ser executadas pelo usuário, não pelo sistema. Os computadores gerenciados podem ficar presos, se Gerenciamento de Atualizações não tiver direitos para reinicializar o computador sem interação manual do usuário.
>
> Para obter mais informações, consulte [definir configurações de política de grupo para atualizações automáticas](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Para Linux, a máquina deve ter acesso a um repositório de atualização. O repositório de atualização pode ser público ou privado. TLS 1.1 ou TLS 1.2 é necessário para interagir com o gerenciamento de atualizações. Um Agente Log Analytics para Linux configurado para reportar a mais de um espaço de trabalho do Log Analytics não é suportado com esta solução.  O computador também deve ter o Python 2. x instalado.

Para obter informações sobre como instalar o agente de Log Analytics para Linux e baixar a versão mais recente, consulte [log Analytics Agent para Linux](https://github.com/microsoft/oms-agent-for-linux). Para obter informações sobre como instalar o agente do Log Analytics para Windows, consulte [Microsoft Monitoring Agent para Windows](../log-analytics/log-analytics-windows-agent.md).

As máquinas virtuais que foram criadas a partir das imagens do Red Hat Enterprise Linux (RHEL) sob demanda que estão disponíveis no Azure Marketplace são registradas para acessar a [Infraestrutura de Atualização do Red Hat (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) que é implantada no Azure. Qualquer distribuição do Linux deve ser atualizada nos repositórios de distribuição de arquivo online seguindo os métodos de distribuição com suporte.

## <a name="permissions"></a>Permissões

Para criar e gerenciar implantações de atualização, você precisa ter permissões específicas. Para saber mais sobre essas permissões, consulte [Acesso baseado em Função - Gerenciamento de Atualizações](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Componentes da solução

A solução consiste nos recursos a seguir. Os recursos são adicionados à sua conta de Automação. Eles são os agentes conectados diretamente ou de um grupo de gerenciamento conectados do Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupos de Hybrid Worker

Depois que você habilita essa solução, qualquer computador com Windows conectado diretamente a seu espaço de trabalho do Log Analytics é automaticamente configurado como um Hybrid Runbook Worker para dar suporte aos runbooks incluídos nessa solução.

Cada computador Windows que é gerenciado pela solução é listado no painel de **grupos de trabalho Hybrid** como um **grupo de trabalho híbrido do Sistema** para a conta de Automação. As soluções usam a convenção de nomenclatura *Hostname FQDN_GUID*. Não é possível direcionar esses grupos com runbooks em sua conta. Elas falham se você tentar. Esses grupos devem dar suporte somente à solução de gerenciamento.

Você pode adicionar os computadores com Windows a um grupo de Hybrid Runbook Worker em sua conta de Automação para dar suporte a runbooks de automação se você usar a mesma conta para a solução e para a associação de grupo do Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="management-packs"></a>Pacotes de gerenciamento

Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager. Esses pacotes de gerenciamento também são instalados em computadores com Windows conectados diretamente depois que você adicionar a solução. Você não precisa configurar ou gerenciar esses pacotes de gerenciamento.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP de Implantação de Atualizações

> [!NOTE]
> Se você tiver um grupo de gerenciamento Operations Manager 1807 ou 2019 com agentes configurados no nível do grupo de gerenciamento para ser associado a um espaço de trabalho, a solução alternativa atual para que eles apareçam é substituir **IsAutoRegistrationEnabled** por **true** em a regra **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Para obter mais informações sobre como os pacotes de gerenciamento de solução são atualizados, consulte [conectar Operations Manager a logs de Azure monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para sistemas com o agente do Operations Manager, para poder ser totalmente gerenciado pelo gerenciamento de atualizações, o agente precisa ser atualizado para o Microsoft Monitoring Agent. Para saber como atualizar o agente, consulte [como atualizar um agente do Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Para ambientes que usam Operations Manager, é necessário que você esteja executando o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="data-collection"></a>Coleta de dados

### <a name="supported-agents"></a>Agentes com suporte

A seguinte tabela descreve as fontes conectadas que têm suporte nessa solução:

| Fonte conectada | Com suporte | Descrição |
| --- | --- | --- |
| Agentes do Windows |SIM |A solução coleta informações sobre atualizações do sistema de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |SIM |A solução coleta informações sobre atualizações do sistema de agentes para Linux e, em seguida, inicia a instalação das atualizações necessárias nas distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |SIM |A solução coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectados.<br/>Não é necessária uma conexão direta do agente de Operations Manager para os logs do Azure Monitor. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência de coleta

Uma verificação é executada duas vezes por dia para cada computador Windows gerenciado. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status foi alterado. Se o status foi alterado, uma verificação de conformidade é iniciada.

Uma verificação é executada a cada hora para cada computador Linux gerenciado.

Pode demorar entre 30 minutos e 6 horas para o painel exibir os dados atualizados dos computadores gerenciados.

A Azure Monitor média registra o uso de dados de um computador usando Gerenciamento de Atualizações é de aproximadamente 25 MB por mês. Esse valor é somente uma aproximação e está sujeito a alterações com base em seu ambiente. É recomendável que você monitore seu ambiente para ver o uso exato que você tem.

## <a name="ports"></a>Planejamento de Rede

Os endereços a seguir são necessários especificamente para gerenciamento de atualizações. A comunicação para esses endereços ocorre pela porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Para computadores Windows, você também deve permitir o tráfego para qualquer ponto de extremidade exigido pelo Windows Update.  Você pode encontrar uma lista atualizada de pontos de extremidade necessários em [problemas relacionados ao http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se você tiver um [servidor de Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)local, também deverá permitir o tráfego para o servidor especificado em sua [chave do WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Para computadores Red Hat Linux, consulte [os IPs para os servidores de distribuição de conteúdo RHUI para os](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) pontos de extremidade necessários. Para outras distribuições do Linux, consulte a documentação do provedor.

Para obter mais informações sobre as portas que exige o Hybrid Runbook Worker, consulte [portas de função do Hybrid Worker](automation-hybrid-runbook-worker.md#hybrid-worker-role).

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, baixe os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Este arquivo é atualizado semanalmente e reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP, é postado semanalmente.

Siga as instruções em [conectar computadores sem acesso à Internet](../azure-monitor/platform/gateway.md) para configurar computadores que não têm acesso à Internet.

## <a name="view-update-assessments"></a>Exibir avaliações de atualização

Na sua conta de Automação, selecione **Gerenciamento de Atualizações** para exibir o status de dos computadores.

Essa exibição fornece informações sobre os computadores, atualizações ausentes, implantações de atualização e implementações de atualização agendada. No **COLUNA DE CONFORMIDADE**, você pode ver a última vez em que a máquina foi avaliada. Na coluna **PREPARAÇÃO DO AGENTE DE ATUALIZAÇÃO**, você pode ver se a integridade do agente de atualização. Se houver um problema, selecione o link para ir para a documentação de solução problemas que pode ajudá-lo a conhecer as etapas necessárias para corrigir o problema.

Para executar uma pesquisa de logs sobre as informações do computador, atualização ou implantação, selecione o item na lista. O painel **Pesquisa de Logs** abre com uma consulta para o item selecionado:

![Exibição padrão do Gerenciamento de Atualizações](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **Atualizações ausentes** para exibir a lista de atualizações que estão faltando nos computadores. Cada atualização é listada e pode ser selecionada. Informações sobre o número de computadores que exigem a atualização, o sistema operacional e um link para obter mais informações são mostradas. O painel de **Pesquisa de Log** mostra mais detalhes sobre as atualizações.

![Atualizações ausentes](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificações de origem

As tabelas a seguir listam as classificações de atualização no Gerenciamento de Atualizações, com uma definição de cada classificação.

### <a name="windows"></a>Windows

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que aborda um bug crítico não relacionado à segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico do produto relacionadas à segurança.        |
|Pacotes cumulativos de atualização     | Um conjunto cumulativo de hotfixes que são reunidos para facilitar a implantação.        |
|Feature packs     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definição     | Uma atualização para vírus ou outros arquivos de definição.        |
|Ferramentas     | Um utilitário ou recurso que ajuda a concluir uma ou mais tarefas.        |
|Atualizações     | Uma atualização para um aplicativo ou arquivo que está atualmente instalado.        |

### <a name="linux-2"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas em atualizações de segurança ou de natureza.        |

Para o Linux, o Gerenciamento de Atualizações pode distinguir entre críticas e atualizações de segurança na nuvem ao exibir dados de avaliação devido a enriquecimento de dados na nuvem. Para aplicação de patch, o Gerenciamento de Atualizações se baseia em dados de classificação disponíveis no computador. Ao contrário de outras distribuições, CentOS não tem essas informações disponíveis fora da caixa. Se você tiver máquinas CentOS configuradas para retornar dados de segurança para o comando a seguir, o Gerenciamento de Atualizações poderá aplicar patch com base em classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não há nenhum método com suporte para habilitar a disponibilidade de dados nativos de classificação em CentOS. Neste momento, somente o suporte de melhor esforço é fornecido aos clientes que podem ter isso habilitado por conta própria.

## <a name="integrate-with-system-center-configuration-manager"></a>Integração com o System Center Configuration Manager

Os clientes que investiram no System Center Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e maturidade do Configuration Manager para ajudá-los a gerenciar as atualizações de software. Configuration Manager é parte de seu ciclo de gerenciamento de atualizações de software (SUM).

Para saber como integrar a solução de gerenciamento ao System Center Configuration Manager, veja [Integrar o System Center Configuration Manager ao Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-patches-on-windows"></a>Patches de terceiros no Windows

Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Este é o WSUS ou o Windows Update. Ferramentas como [System Center Updates Publisher](/sccm/sum/tools/updates-publisher) (Updates Publisher) permitem que você publique atualizações personalizadas no WSUS. Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam System Center Configuration Manager como seu repositório de atualizações com software de terceiros. Para saber como configurar o Updates Publisher, veja [Instalar o Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Aplicar patch em computadores Linux

As seções a seguir explicam possíveis problemas com aplicação de patch do Linux.

### <a name="unexpected-os-level-upgrades"></a>Atualizações de nível de sistema operacional inesperadas

Em algumas variantes do Linux, como o Red Hat Enterprise Linux, as atualizações no nível do SO podem ocorrer através de pacotes. Isso pode originar execuções de Gerenciamento de Atualizações onde o número de versão do SO é alterado. Como o Gerenciamento de Atualizações usa os mesmos métodos para atualizar pacotes que um administrador usaria localmente no computador Linux, esse comportamento é intencional.

Para evitar atualização da versão do SO por meio de execuções do Gerenciamento de Atualizações, utilize o recurso **Exclusão**.

No Red Hat Enterprise Linux, o nome do pacote a ser excluído seria: redhat-release-server.x86_64.

![Pacotes a serem excluídos do Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Patches críticos / de segurança não são aplicados

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Isso filtra as atualizações aplicadas à máquina que atendem aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como Gerenciamento de Atualizações executa o enriquecimento de atualização na nuvem, algumas atualizações podem ser sinalizadas em Gerenciamento de Atualizações como tendo impacto sobre a segurança, mesmo que o computador local não tenha essas informações. Como resultado, se você aplicar atualizações críticas a um computador Linux, pode haver atualizações, que não estão marcadas como tendo impacto de segurança nesse computador e as atualizações não são aplicadas.

No entanto, o Gerenciamento de Atualizações ainda pode relatar que o computador não é compatível porque possui informações adicionais sobre a atualização relevante.

Implantar atualizações por classificação de atualização não funciona no CentOS pronto para uso. Para implantar corretamente atualizações para CentOS, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para SUSE, selecionar *somente* 'Outras atualizações', já que a classificação pode resultar em que algumas atualizações de segurança também sejam instaladas se as atualizações de segurança relacionados ao zypper (gerenciamento de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, talvez você precise executar novamente a implantação de atualização. Para verificar, veja o log de atualização.

### <a name="multi-tenant"></a>Implantações de Atualização entre locatários

Se você tiver máquinas em outro locatário do Azure relatando ao Gerenciamento de Atualizações que você precisa aplicar patches, você precisará usar a seguinte solução alternativa para agendá-los. Use o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com a opção `-ForUpdate` para criar um agendamento e use o cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passe os computadores no outro locatário para o parâmetro `-NonAzureComputer`. O seguinte exemplo mostra um exemplo de como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Habilitar Gerenciamento de Atualizações

Para começar a aplicar patch em sistemas, você precisa habilitar a solução de Gerenciamento de Atualizações. Há muitas maneiras de integrar computadores ao Gerenciamento de Atualizações. A seguir estão as maneiras recomendadas e compatíveis de integrar a solução:

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [Da navegação em várias máquinas](automation-onboard-solutions-from-browse.md)
* [Da sua conta de Automação](automation-onboard-solutions-from-automation-account.md)
* [Com um runbook de Automação do Azure](automation-onboard-solutions.md)

## <a name="next-steps"></a>Próximos passos

Continue no tutorial para saber como gerenciar atualizações para as máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerenciar atualizações e patches para VMs do Microsoft Azure](automation-tutorial-update-management.md)

* Use pesquisas de log em [logs de Azure monitor](../log-analytics/log-analytics-log-searches.md) para exibir dados de atualização detalhados.
* [Criar alertas](automation-tutorial-update-management.md#configure-alerts) para status de implantação de atualização.

* Para aprender a interagir com o Gerenciamento de Atualizações por meio da API REST, consulte [Configurações de atualização de software](/rest/api/automation/softwareupdateconfigurations)
* Para saber como solucionar problemas no seu Gerenciamento de Atualizações, consulte [Solucionar problemas no Gerenciador de Atualizações](troubleshoot/update-management.md)
