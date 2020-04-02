---
title: Visão geral do agente do Log Analytics
description: Este tópico ajuda o reconhecimento de como coletar dados e monitorar computadores hospedados no Azure, no local ou em outro ambiente de nuvem com o Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: d52d8e6d0f6e3325b5c5cdc9a2e21654e6a2b621
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520732"
---
# <a name="log-analytics-agent-overview"></a>Visão geral do agente do Log Analytics
O agente Azure Log Analytics foi desenvolvido para gerenciamento abrangente em máquinas virtuais em qualquer nuvem, máquinas locais e aquelas monitoradas pelo [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/). Os agentes do Windows e Linux enviam dados coletados de diferentes fontes para o espaço de trabalho do Log Analytics no Azure Monitor, bem como quaisquer logs ou métricas exclusivos definidos em uma solução de monitoramento. O agente log analytics também suporta insights e outros serviços no Azure Monitor, como [o Azure Monitor for VMs,](../insights/vminsights-enable-overview.md) [Azure Security Center](/azure/security-center/)e [Azure Automation.](../../automation/automation-intro.md)

Este artigo fornece uma visão geral detalhada do agente, dos requisitos do sistema e da rede e dos diferentes métodos de implantação.

> [!NOTE]
> Você também pode ver o agente log analytics referido como o Microsoft Monitoring Agent (MMA) ou agente DO OMS Linux.

> [!NOTE]
> A extensão azure Diagnostics é um dos agentes disponíveis para coletar dados de monitoramento do sistema operacional convidado de recursos computacionais. Consulte [a visão geral dos agentes do Monitor do Azure](agents-overview.md) para obter uma descrição dos diferentes agentes e orientações sobre a seleção dos agentes apropriados para suas necessidades.

## <a name="comparison-to-azure-diagnostics-extension"></a>Comparação com a extensão de diagnósticos do Azure
A [extensão de diagnóstico do Azure](diagnostics-extension-overview.md) no Azure Monitor também pode ser usada para coletar dados de monitoramento do sistema operacional convidado de máquinas virtuais Azure. Você pode optar por usar um ou ambos, dependendo de suas necessidades. Consulte [a visão geral dos agentes do Monitor Do Azure](agents-overview.md) para obter uma comparação detalhada dos agentes do Monitor Do Azure. 

As principais diferenças a considerar são:

- A extensão azure diagnostics pode ser usada apenas com máquinas virtuais Azure. O agente Log Analytics pode ser usado com máquinas virtuais no Azure, outras nuvens e no local.
- A extensão do Azure Diagnostics envia dados para o Azure Storage, [Azure Monitor Metrics](data-platform-metrics.md) (somente Windows) e Event Hubs. O agente Log Analytics coleta dados para [o Azure Monitor Logs](data-platform-logs.md).
- O agente Log Analytics é necessário para [soluções,](../monitor-reference.md#insights-and-core-solutions) [Monitor Azure para VMs](../insights/vminsights-overview.md)e outros serviços, como [o Azure Security Center.](/azure/security-center/)

## <a name="costs"></a>Custos
Não há custo para o agente log analytics, mas você pode incorrer em encargos pelos dados ingeridos. Verifique [gerenciar o uso e os custos com o Azure Monitor Logs](manage-cost-storage.md) para obter informações detalhadas sobre os preços dos dados coletados em um espaço de trabalho do Log Analytics.

## <a name="data-collected"></a>Dados coletados
A tabela a seguir lista os tipos de dados que você pode configurar um espaço de trabalho do Log Analytics para coletar de todos os agentes conectados. Veja [O que é monitorado pelo Azure Monitor?](../monitor-reference.md) para uma lista de insights, soluções e outras soluções que usam o agente Log Analytics para coletar outros tipos de dados.

| fonte de dados | Descrição |
| --- | --- |
| [Registros de eventos do Windows](data-sources-windows-events.md) | Informações enviadas ao sistema de registro de evento do Windows. |
| [Syslog](data-sources-syslog.md)                     | Informações enviadas para o sistema de registro de eventos Linux. |
| [Desempenho](data-sources-performance-counters.md)  | Valores numéricos medindo o desempenho de diferentes aspectos do sistema operacional e cargas de trabalho. |
| [Registros IIS](data-sources-iis-logs.md)                 | Informações de uso para sites iis em execução no sistema operacional convidado. |
| [Logs personalizados](data-sources-custom-logs.md)           | Eventos de arquivos de texto em computadores Windows e Linux. |

## <a name="data-destinations"></a>Destinos de dados
O agente do Log Analytics envia dados para um espaço de trabalho do Log Analytics no Azure Monitor. O agente do Windows pode ser multiabrigado para enviar dados para vários espaços de trabalho e grupos de gerenciamento do System Center Operations Manager. O agente Linux pode enviar para apenas um único destino.

## <a name="other-services"></a>Outros serviços
O agente para Linux e Windows não é apenas para se conectar ao Azure Monitor, ele também suporta a Automação Azure para hospedar a função de trabalhador do Hybrid Runbook e outros serviços como Rastreamento de [Alterações, Gerenciamento de Atualizações](../../automation/automation-update-management.md)e [Centro de Segurança do Azure.](../../security-center/security-center-intro.md) [Change Tracking](../../automation/change-tracking.md) Para obter mais informações sobre a função Hybrid Runbook Worker, consulte [Hybrid Runbook Worker de Automação do Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalação e configuração

Ao usar os agentes do Log Analytics para coletar dados, você precisa entender o seguinte para planejar a implantação do seu agente:

* Para coletar dados de agentes do Windows, você pode [configurar cada agente para reportar a um ou mais espaços de trabalho,](agent-windows.md)mesmo enquanto ele está reportando a um grupo de gerenciamento do System Center Operations Manager. O agente do Windows pode reportar até quatro espaços de trabalho.
* O agente Linux não suporta multi-homing e só pode reportar a um único espaço de trabalho.
* O agente do Windows suporta o [padrão FIPS 140,](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)enquanto o agente Linux não o suporta.  

Se você estiver usando o System Center Operations Manager 2012 R2 ou posterior:

* Cada grupo de gerenciamento do Gerente de Operações pode ser [conectado a apenas um espaço de trabalho](om-agents.md).
* Os computadores Linux reportados a um grupo de gerenciamento devem ser configurados para reportar diretamente a um espaço de trabalho do Log Analytics. Se seus computadores Linux já estiverem reportando diretamente a um espaço de trabalho e você quiser monitorá-los com o Gerente de Operações, siga essas etapas [para reportar a um grupo de gerenciamento do Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Você pode instalar o agente Log Analytics Windows no computador Windows e fazê-lo relatar tanto ao Operations Manager integrado com um espaço de trabalho quanto a um espaço de trabalho diferente.


Existem vários métodos para instalar o agente Log Analytics e conectar sua máquina ao Monitor Azure, dependendo de suas necessidades. A tabela a seguir realça cada método para determinar o que funciona melhor em sua organização.

|Fonte | Método | Descrição|
|-------|-------------|-------------|
|VM do Azure| [Manualmente do portal Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Especifique As VMs a serem implantadas no espaço de trabalho do Log Analytics. |
| | Extensão de VM do Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) usando o Azure CLI ou com um modelo do Azure Resource Manager | A extensão instala o agente do Log Analytics nas máquinas virtuais do Azure e as registra em um workspace do Azure Monitor existente. |
| | [Azure Monitor para VMs](../insights/vminsights-enable-overview.md) | Quando você habilita o monitoramento com o Azure Monitor para VMs, ele instala a extensão e o agente do Log Analytics. |
| | [Provisionamento automático do Azure Security Center](../../security-center/security-center-enable-data-collection.md) | O Azure Security Center pode provisionar o agente Log Analytics em todas as VMs Azure suportadas e quaisquer novas que forem criadas se você habilitar que ele monitore vulnerabilidades e ameaças de segurança. Se ativado, qualquer VM nova ou existente sem um agente instalado será provisionada. |
| Computador Windows híbrido| [Instalação manual](agent-windows.md) | Instale o agente microsoft monitoring a partir da linha de comando. |
| | [DSC de Automação do Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatize a instalação com o Azure Automation DSC. |
| | [Modelo do Resource Manager com o Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Use um modelo do Azure Resource Manager se você tiver implantado o Microsoft Azure Stack em seu data center.| 
| Computador Linux híbrido| [Instalação manual](../../azure-monitor/learn/quick-collect-linux-computer.md)|Instale o agente para Linux chamando um script de wrapper hospedado no GitHub. | 
| System Center Operations Manager|[Conectar o Operations Manager ao Log Analytics](../../azure-monitor/platform/om-agents.md) | Configure a integração entre os logs do Operations Manager e do Azure Monitor para encaminhar dados coletados de computadores Windows reportando a um grupo de gerenciamento.|  


## <a name="supported-windows-operating-systems"></a>Sistemas operacionais Windows compatíveis

Há suporte oficial para as seguintes versões do sistema operacional Windows para o agente para Windows:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, versão 1709 e 1803
* Windows 7 SP1, Windows 8 Enterprise e Pro, e Windows 10 Enterprise e Pro

>[!NOTE]
>Embora o agente de Análise de Log para Windows tenha sido projetado para suportar cenários de monitoramento de servidor, percebemos que você pode executar o cliente Windows para suportar cargas de trabalho configuradas e otimizadas para o sistema operacional do servidor. O agente oferece suporte ao cliente Windows, no entanto, nossas soluções de monitoramento não se concentram em cenários de monitoramento de clientes, a menos que explicitamente declarados.

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

Esta seção fornece detalhes sobre as distribuições de Linux com suporte.

Começando com versões lançadas depois de agosto de 2018, estamos fazendo as seguintes alterações ao nosso modelo de suporte:  

* Somente o servidor versões têm suporte, um não cliente.  
* Foco no suporte a qualquer um dos [distros endossados pelo Azure Linux](../../virtual-machines/linux/endorsed-distros.md). Observe que pode haver algum atraso entre uma nova distro/versão ser Azure Linux Endorsed e ser suportado para o agente Log Analytics Linux.
* Todas as versões secundárias têm suporte para cada versão principal listada.
* As versões que passaram a data de fim de suporte do fabricante não são suportadas.  
* Não há suporte para novas versões do AMI.  
* Apenas versões que executam o SSL 1.x por padrão são suportadas.

>[!NOTE]
>Se você estiver usando uma distribuição ou versão que não é suportada no momento e não se alinha ao nosso modelo de suporte, recomendamos que você distribua esse repositório, reconhecendo que o suporte da Microsoft não fornecerá assistência com as versões do agente bifurcado.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86 x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x86 x64) 
* Red Hat Enterprise Linux Server 6 (x86 x64) e 7 (x64)
* Debian GNU/Linux 8 e 9 (x86 x64)
* Ubuntu 14.04 18.04 LTS (x64), 16.04 LTS (x86 x64) e LTS (x86 x64)
* SUSE Linux Enterprise Server 12 (x64) e 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 só tem suporte em plataformas de x86_x64 (64 bits) e OpenSSL mais cedo do que 1. x não tem suporte em qualquer plataforma.
>

### <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela a seguir destaca os pacotes necessários para distros Linux suportados em que o agente será instalado.

|Pacote necessário |Descrição |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|Openssl    | Bibliotecas OpenSSL | 1.0.x ou 1.1.x |
|Curl | cliente Web cURL | 7.15.5 |
|Python-ctypes | | 
|PAM | Módulos de autenticação conectáveis | | 

>[!NOTE]
>Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para os logs do Monitor Do Azure, encorajamos fortemente que você configure o agente para usar pelo menos o TLS (Transport Layer Security, segurança de camada de transporte) 1.2. Constatou-se que versões mais antigas do protocolo TLS/protocolo SSL eram vulneráveis e embora elas ainda funcionem no momento para permitir a compatibilidade com versões anteriores, elas **não são recomendadas**.  Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Requisito de suporte de assinatura de código SHA-2 para windows
O agente do Windows começará a usar exclusivamente a assinatura SHA-2 em 18 de maio de 2020. Essa mudança afetará os clientes que usam o agente Log Analytics em um sistema operacional legado como parte de qualquer serviço do Azure (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP). A alteração não requer nenhuma ação do cliente, a menos que você esteja executando o agente em uma versão do SISTEMA OPERACIONAL legado (Windows 7, Windows Server 2008 R2 e Windows Server 2008). Os clientes que executam uma versão do Sistema Operacional legado são obrigados a tomar as seguintes ações em suas máquinas antes de 18 de maio de 2020 ou seus agentes pararão de enviar dados para seus espaços de trabalho do Log Analytics:

1. Instale o service pack mais recente para o seu sistema operacional. As versões necessárias do service pack são:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Instale as atualizações do Windows de assinatura SHA-2 para o seu SISTEMA OPERACIONAL, conforme descrito no requisito de [suporte de assinatura de código SHA-2 para Windows e WSUS de 2019](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Atualizar para a versão mais recente do agente windows (versão 10.20.18029).
4. Recomenda-se configurar o agente para [usar o TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Requisitos de rede
O agente para Linux e Windows comunica-se de saída para o serviço Azure Monitor através da porta TCP 443, e se a máquina se conectar através de um firewall ou servidor proxy para se comunicar pela Internet, revise os requisitos abaixo para entender a configuração de rede necessária. Se suas políticas de segurança de TI não permitirem que computadores na rede se conectem à Internet, você pode configurar um [gateway log analytics](gateway.md) e, em seguida, configurar o agente para se conectar através do gateway aos logs do Azure Monitor. O agente pode então receber informações de configuração e enviar dados coletados dependendo das regras de coleta de dados e soluções de monitoramento que você habilitou em seu espaço de trabalho.

![Diagrama de comunicação do agente do Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

A tabela a seguir lista as informações de configuração de proxy e firewall necessárias para que os agentes Linux e Windows se comuniquem com os logs do Monitor do Azure.

### <a name="firewall-requirements"></a>Requisitos de firewall

|Recurso de agente|Portas |Direção |Ignorar a inspeção de HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Entrada e saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Entrada e saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Entrada e saída|Sim |
|*.azure-automation.net |Porta 443 |Entrada e saída|Sim |
|*.azure.com |Porta 443|Entrada e saída|Sim |

Para obter informações de firewall necessárias para o Governo Do Azure, consulte [a gestão do governo do Azure](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Se você planeja usar o Azure Automation Hybrid Runbook Worker para se conectar e se registrar no serviço de Automação para usar runbooks ou soluções de gerenciamento em seu ambiente, ele deve ter acesso ao número da porta e às URLs descritas no [Configure sua rede para o Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Configuração de Proxy

O agente Windows e Linux suporta a comunicação através de um servidor proxy ou do gateway Log Analytics para o Azure Monitor usando o protocolo HTTPS.  Há suporte para a autenticação anônima e básica (nome de usuário/senha).  Para o agente Windows conectado diretamente ao serviço, a configuração proxy é especificada durante a instalação ou [após a implantação](agent-manage.md#update-proxy-settings) do Painel de Controle ou com o PowerShell.  

Para o agente Linux, o servidor proxy pode ser especificado durante a instalação ou modificando o arquivo de configuração proxy.conf [após a instalação](agent-manage.md#update-proxy-settings).  O valor de configuração de proxy do agente do Linux tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se seu servidor proxy não exigir autenticação, o agente para Linux exigirá mesmo assim fornecendo um pseudo usuário/senha. Isso pode ser qualquer nome de usuário ou senha.

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | HTTPS |
|usuário | Nome de usuário opcional para autenticação de proxy |
|password | Senha opcional para autenticação de proxy |
|proxyhost | Endereço ou FQDN do servidor proxy/gateway do Log Analytics |
|porta | Número da porta opcional para o servidor proxy/gateway do Log Analytics |

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se você usar caracteres\@especiais como " " em sua senha, você receberá um erro de conexão proxy porque o valor é analisado incorretamente.  Para contornar esse problema, codifique a senha na URL usando uma ferramenta como [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Próximas etapas

* Consultar as [fontes de dados](agent-data-sources.md) para entender as fontes de dados disponíveis para coletar dados do sistema Windows ou Linux. 
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 
* Conheça as [soluções de monitoramento](../insights/solutions.md) que adicionam funcionalidade ao Azure Monitor e também coletam dados no espaço de trabalho do Log Analytics.

