---
title: Visão geral do agente de Log Analytics
description: Este tópico ajuda o reconhecimento de como coletar dados e monitorar computadores hospedados no Azure, no local ou em outro ambiente de nuvem com o Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 08c80b0a37e21d53fc974bd878501b9326c9449b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207217"
---
# <a name="log-analytics-agent-overview"></a>Visão geral do agente de Log Analytics
O agente de Log Analytics do Azure foi desenvolvido para gerenciamento abrangente em máquinas virtuais em qualquer nuvem, em máquinas locais e aquelas monitoradas pelo [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/). Os agentes do Windows e Linux enviam dados coletados de fontes diferentes para seu espaço de trabalho do Log Analytics no Azure Monitor, bem como quaisquer logs ou métricas exclusivos, conforme definido em uma solução de monitoramento. O agente de Log Analytics também dá suporte a informações e outros serviços em Azure Monitor como [Azure monitor para VMs](../insights/vminsights-enable-overview.md), [central de segurança do Azure](/azure/security-center/)e [automação do Azure](../../automation/automation-intro.md).

Este artigo fornece uma visão geral detalhada do agente, dos requisitos do sistema e da rede e dos diferentes métodos de implantação.

> [!NOTE]
> Você também pode ver o agente de Log Analytics referido como o Microsoft Monitoring Agent (MMA) ou o agente do OMS Linux.

> [!NOTE]
> A extensão de Diagnóstico do Azure é um dos agentes disponíveis para coletar dados de monitoramento do sistema operacional convidado dos recursos de computação. Consulte [visão geral dos agentes de Azure monitor](agents-overview.md) para obter uma descrição dos diferentes agentes e diretrizes sobre como selecionar os agentes apropriados para seus requisitos.

## <a name="comparison-to-azure-diagnostics-extension"></a>Comparação com a extensão de diagnóstico do Azure
A [extensão de diagnóstico do Azure](diagnostics-extension-overview.md) no Azure monitor também pode ser usada para coletar dados de monitoramento do sistema operacional convidado de máquinas virtuais do Azure. Você pode optar por usar um ou ambos, dependendo de seus requisitos. Consulte [visão geral dos agentes de Azure monitor](agents-overview.md) para obter uma comparação detalhada dos agentes de Azure monitor. 

As principais diferenças a serem consideradas são:

- A extensão Diagnóstico do Azure pode ser usada somente com máquinas virtuais do Azure. O agente de Log Analytics pode ser usado com máquinas virtuais no Azure, em outras nuvens e no local.
- A extensão de Diagnóstico do Azure envia dados para o armazenamento do Azure, [métricas de Azure monitor](data-platform-metrics.md) (somente Windows) e hubs de eventos. O agente de Log Analytics coleta dados para [Azure monitor logs](data-platform-logs.md).
- O agente de Log Analytics é necessário para [soluções](../monitor-reference.md#insights-and-core-solutions), [Azure monitor para VMs](../insights/vminsights-overview.md)e outros serviços, como a [central de segurança do Azure](/azure/security-center/).

## <a name="costs"></a>Custos
Não há nenhum custo para Log Analytics Agent, mas você pode incorrer em encargos pelos dados ingeridos. Marque [gerenciar o uso e os custos com os logs de Azure monitor](manage-cost-storage.md) para obter informações detalhadas sobre os preços dos dados coletados em um espaço de trabalho log Analytics.

## <a name="data-collected"></a>Dados coletados
A tabela a seguir lista os tipos de dados que você pode configurar um espaço de trabalho Log Analytics para coletar de todos os agentes conectados. Veja [o que é monitorado pelo Azure monitor?](../monitor-reference.md) para obter uma lista de informações, soluções e outras soluções que usam o agente de log Analytics para coletar outros tipos de dados.

| fonte de dados | Descrição |
| --- | --- |
| [Logs de eventos do Windows](data-sources-windows-events.md) | Informações enviadas ao sistema de registro de evento do Windows. |
| [syslog](data-sources-syslog.md)                     | Informações enviadas para o sistema de log de eventos do Linux. |
| [Desempenho](data-sources-performance-counters.md)  | Valores numéricos que medem o desempenho de diferentes aspectos do sistema operacional e das cargas de trabalho. |
| [Logs do IIS](data-sources-iis-logs.md)                 | Informações de uso para sites do IIS em execução no sistema operacional convidado. |
| [Logs personalizados](data-sources-custom-logs.md)           | Eventos de arquivos de texto em computadores Windows e Linux. |

## <a name="data-destinations"></a>Destinos de dados
O agente de Log Analytics envia dados para um espaço de trabalho do Log Analytics no Azure Monitor. O agente do Windows pode ser de hospedagem múltipla para enviar dados para vários espaços de trabalho e System Center Operations Manager grupos de gerenciamento. O agente do Linux pode enviar para apenas um único destino.

## <a name="other-services"></a>Outros serviços
O agente para Linux e Windows não é apenas para se conectar ao Azure Monitor, ele também dá suporte à automação do Azure para hospedar a função de Hybrid runbook Worker e outros serviços, como [controle de alterações](../../automation/change-tracking.md), [Gerenciamento de atualizações](../../automation/automation-update-management.md)e [central de segurança do Azure](../../security-center/security-center-intro.md). Para obter mais informações sobre a função Hybrid Runbook Worker, consulte [Hybrid Runbook Worker de Automação do Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalação e configuração

Ao usar os agentes de Log Analytics para coletar dados, você precisa entender o seguinte para planejar a implantação do agente:

* Para coletar dados de agentes do Windows, você pode [configurar cada agente para reportar para um ou mais espaços de trabalho](agent-windows.md), mesmo enquanto estiver relatando a um grupo de gerenciamento de System Center Operations Manager. O agente do Windows pode relatar até quatro espaços de trabalho.
* O agente do Linux não dá suporte a hospedagem múltipla e só pode relatar para um único espaço de trabalho.
* O agente do Windows dá suporte ao [padrão FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), enquanto o agente do Linux não oferece suporte a ele.  

Se você estiver usando o System Center Operations Manager 2012 R2 ou posterior:

* Cada grupo de gerenciamento de Operations Manager pode ser [conectado a apenas um espaço de trabalho](om-agents.md).
* Os computadores Linux que relatam para um grupo de gerenciamento devem ser configurados para relatar diretamente a um espaço de trabalho Log Analytics. Se os computadores Linux já estiverem se reportando diretamente a um espaço de trabalho e você quiser monitorá-los com Operations Manager, siga estas etapas para [relatar a um grupo de gerenciamento de Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Você pode instalar o Log Analytics agente do Windows no computador Windows e fazer com que ele se reporte a ambos Operations Manager integrados a um espaço de trabalho e a um espaço de trabalho diferente.


Há vários métodos para instalar o agente de Log Analytics e conectar seu computador ao Azure Monitor dependendo de seus requisitos. A tabela a seguir realça cada método para determinar o que funciona melhor em sua organização.

|Fonte | Método | Descrição|
|-------|-------------|-------------|
|VM do Azure| [Manualmente a partir do portal do Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Especifique as VMs a serem implantadas a partir do espaço de trabalho Log Analytics. |
| | Log Analytics extensão de VM para [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) usando CLI do Azure ou com um modelo de Azure Resource Manager | A extensão instala o agente do Log Analytics nas máquinas virtuais do Azure e as registra em um workspace do Azure Monitor existente. |
| | [Azure Monitor para VMs](../insights/vminsights-enable-overview.md) | Quando você habilita o monitoramento com Azure Monitor para VMs, ele instala a extensão e o agente do Log Analytics. |
| | [Provisionamento automático da central de segurança do Azure](../../security-center/security-center-enable-data-collection.md) | A central de segurança do Azure pode provisionar o agente de Log Analytics em todas as VMs do Azure com suporte e quaisquer novas que forem criadas se você habilitá-la para monitorar vulnerabilidades de segurança e ameaças. Se habilitada, qualquer VM nova ou existente sem um agente instalado será provisionada. |
| Computador Windows híbrido| [Instalação manual](agent-windows.md) | Instale o Microsoft Monitoring Agent na linha de comando. |
| | [DSC de Automação do Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatize a instalação com o Azure DSC de Automação. |
| | [Modelo do Resource Manager com o Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Use um modelo de Azure Resource Manager se você implantou o Microsoft Azure Stack em seu datacenter.| 
| Computador Linux híbrido| [Instalação manual](../../azure-monitor/learn/quick-collect-linux-computer.md)|Instale o agente para Linux chamando um script de wrapper hospedado no GitHub. | 
| System Center Operations Manager|[Conectar o Operations Manager ao Log Analytics](../../azure-monitor/platform/om-agents.md) | Configure a integração entre Operations Manager e Azure Monitor logs para encaminhar os dados coletados de computadores com Windows relatando a um grupo de gerenciamento.|  


## <a name="supported-windows-operating-systems"></a>Sistemas operacionais Windows compatíveis

Há suporte oficial para as seguintes versões do sistema operacional Windows para o agente para Windows:

* Windows Server 2019
* Windows Server 2016, versão 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise e pro
* Windows 8 Enterprise e pro 
* Windows 7 SP1

>[!NOTE]
>Embora o agente Log Analytics para Windows tenha sido projetado para dar suporte a cenários de monitoramento de servidor, percebemos que você pode executar o cliente Windows para dar suporte a cargas de trabalho configuradas e otimizadas para o sistema operacional do servidor. O agente oferece suporte ao Windows Client, mas nossas soluções de monitoramento não se concentram em cenários de monitoramento de clientes, a menos que explicitamente declarado.

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

Esta seção fornece detalhes sobre as distribuições de Linux com suporte.

Começando com versões lançadas depois de agosto de 2018, estamos fazendo as seguintes alterações ao nosso modelo de suporte:  

* Somente o servidor versões têm suporte, um não cliente.  
* Concentre o suporte em qualquer um dos [distribuições endossados do Linux do Azure](../../virtual-machines/linux/endorsed-distros.md). Observe que pode haver algum atraso entre uma nova distribuição/versão sendo endossada pelo Linux do Azure e há suporte para o agente do Log Analytics Linux.
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

A tabela a seguir realça os pacotes necessários para distribuições do Linux com suporte em que o agente será instalado.

|Pacote necessário |Descrição |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|Openssl    | Bibliotecas OpenSSL | 1,0. x ou 1.1. x |
|Curl | cliente Web cURL | 7.15.5 |
|Python-ctypes | | 
|PAM | Módulos de autenticação conectáveis | | 

>[!NOTE]
>Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para Azure Monitor logs, é altamente recomendável configurar o agente para usar pelo menos o protocolo TLS 1,2. Constatou-se que versões mais antigas do protocolo TLS/protocolo SSL eram vulneráveis e embora elas ainda funcionem no momento para permitir a compatibilidade com versões anteriores, elas **não são recomendadas**.  Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Requisito de suporte à assinatura de código SHA-2 para Windows
O agente do Windows começará a usar exclusivamente a assinatura SHA-2 em 18 de maio de 2020. Essa alteração afetará os clientes que usam o agente de Log Analytics em um sistema operacional herdado como parte de qualquer serviço do Azure (Azure Monitor, automação do Azure, Azure Gerenciamento de Atualizações, Azure Controle de Alterações, central de segurança do Azure, Azure Sentinel, Windows Defender ATP). A alteração não requer nenhuma ação do cliente, a menos que você esteja executando o agente em uma versão do sistema operacional herdada (Windows 7, Windows Server 2008 R2 e Windows Server 2008). Os clientes em execução em uma versão de sistema operacional herdada são necessários para executar as seguintes ações em suas máquinas antes de 18 de maio de 2020 ou seus agentes deixarão de enviar dados para seus espaços de trabalho de Log Analytics:

1. Instale o Service Pack mais recente para seu sistema operacional. As versões de service pack necessárias são:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Instale as atualizações do Windows de assinatura do SHA-2 para seu sistema operacional conforme descrito em [requisito de suporte à assinatura de código do 2019 SHA-2 para Windows e WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Atualize para a versão mais recente do agente do Windows (versão 10.20.18029).
4. Recomendado para configurar o agente para [usar o TLS 1,2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Requisitos de rede
O agente para Linux e Windows comunica a saída para o serviço de Azure Monitor pela porta TCP 443 e, se o computador se conecta por meio de um firewall ou servidor proxy para se comunicar pela Internet, examine os requisitos abaixo para entender a configuração de rede necessária. Se as suas políticas de segurança de ti não permitirem que computadores na rede se conectem à Internet, você poderá configurar um [Gateway de log Analytics](gateway.md) e, em seguida, configurar o agente para se conectar por meio do gateway para Azure monitor logs. O agente pode receber informações de configuração e enviar dados coletados, dependendo de quais regras de coleta de dados e soluções de monitoramento você habilitou em seu espaço de trabalho.

![Diagrama de comunicação do agente do Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

A tabela a seguir lista as informações de configuração de proxy e firewall necessárias para que os agentes do Linux e do Windows se comuniquem com os logs de Azure Monitor.

### <a name="firewall-requirements"></a>Requisitos de firewall

|Recurso de agente|Portas |Direção |Ignorar a inspeção de HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Entrada e saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Entrada e saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Entrada e saída|Sim |
|*.azure-automation.net |Porta 443 |Entrada e saída|Sim |
|*.azure.com |Porta 443|Entrada e saída|Sim |

Para obter informações de firewall necessárias para o Azure governamental, consulte [Gerenciamento do Azure governamental](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Se você planeja usar o Hybrid Runbook Worker de automação do Azure para se conectar e se registrar no serviço de automação para usar runbooks ou soluções de gerenciamento em seu ambiente, ele deve ter acesso ao número da porta e às URLs descritas em [configurar sua rede para o Hybrid runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Configuração de Proxy

O agente do Windows e Linux dá suporte à comunicação por meio de um servidor proxy ou Log Analytics gateway para Azure Monitor usando o protocolo HTTPS.  Há suporte para a autenticação anônima e básica (nome de usuário/senha).  Para o agente do Windows conectado diretamente ao serviço, a configuração de proxy é especificada durante a instalação ou [após a implantação](agent-manage.md#update-proxy-settings) no painel de controle ou com o PowerShell.  

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
> Se você usar caracteres especiais como "\@" em sua senha, receberá um erro de conexão de proxy porque o valor é analisado incorretamente.  Para contornar esse problema, codifique a senha na URL usando uma ferramenta como [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Próximas etapas

* Consultar as [fontes de dados](agent-data-sources.md) para entender as fontes de dados disponíveis para coletar dados do sistema Windows ou Linux. 
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 
* Conheça as [soluções de monitoramento](../insights/solutions.md) que adicionam funcionalidade ao Azure Monitor e também coletam dados no espaço de trabalho do Log Analytics.

