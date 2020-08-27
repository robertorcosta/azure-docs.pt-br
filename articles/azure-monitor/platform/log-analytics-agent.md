---
title: Visão geral do Agente do Log Analytics
description: Este tópico ajuda o reconhecimento de como coletar dados e monitorar computadores hospedados no Azure, no local ou em outro ambiente de nuvem com o Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2020
ms.openlocfilehash: e1fa2fe11873d08fae5add1ee3206f6f887975eb
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88960910"
---
# <a name="log-analytics-agent-overview"></a>Visão geral do Agente do Log Analytics
O Agente do Log Analytics do Azure foi desenvolvido para um gerenciamento abrangente entre máquinas virtuais em qualquer nuvem, máquinas locais ou aquelas monitoradas pelo [System Center Operations Manager](/system-center/scom/). Os agentes do Windows e do Linux enviam dados coletados de diferentes fontes ao seu workspace do Log Analytics no Azure Monitor, bem como outros logs ou métricas exclusivos, conforme definido em uma solução de monitoramento. O Agente do Log Analytics também dá suporte a informações e outros serviços no Azure Monitor, por exemplo, [Azure Monitor para VMs](../insights/vminsights-enable-overview.md), [Central de Segurança do Azure](../../security-center/index.yml) e [Automação do Azure](../../automation/automation-intro.md).

Este artigo fornece uma visão geral detalhada do agente, dos requisitos do sistema e da rede e dos diferentes métodos de implantação.

> [!NOTE]
> Você também pode ver o Agente do Log Analytics mencionado como MMA (Microsoft Monitoring Agent) ou Agente do OMS para Linux.

> [!NOTE]
> A extensão de Diagnóstico do Azure é um dos agentes disponíveis para coletar dados de monitoramento do sistema operacional convidado dos recursos de computação. Consulte [Visão geral dos agentes do Azure Monitor ](agents-overview.md) para obter uma descrição dos diferentes agentes e diretrizes sobre como selecionar os agentes apropriados para seus requisitos.

## <a name="comparison-to-azure-diagnostics-extension"></a>Comparação com a extensão de Diagnóstico do Azure
A [extensão de Diagnóstico do Azure](diagnostics-extension-overview.md) no Azure Monitor também pode ser usada para coletar dados de monitoramento do sistema operacional convidado de máquinas virtuais do Azure. Você pode optar por usar um ou ambos, dependendo dos requisitos. Consulte [Visão geral dos agentes do Azure Monitor](agents-overview.md) para obter uma comparação detalhada dos agentes do Azure Monitor. 

As principais diferenças a serem consideradas são:

- A extensão de Diagnóstico do Azure somente pode ser usada com máquinas virtuais do Azure. O Agente do Log Analytics pode ser usado com máquinas virtuais no Azure, em outras nuvens e localmente.
- A extensão de Diagnóstico do Azure envia dados para o Armazenamento do Azure, as [Métricas do Azure Monitor](data-platform-metrics.md) (somente Windows) e os Hubs de Eventos. O Agente do Log Analytics coleta dados para os [logs do Azure Monitor](data-platform-logs.md).
- O Agente do Log Analytics é obrigatório para [soluções](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor para VMs](../insights/vminsights-overview.md) e outros serviços, como a [Central de Segurança do Azure](../../security-center/index.yml).

## <a name="costs"></a>Custos
Não há nenhum custo para o Agente do Log Analytics, mas você pode incorrer em encargos por conta dos dados ingeridos. Confira [Gerenciar o uso e os custos com os Logs do Azure Monitor](manage-cost-storage.md) para obter informações detalhadas sobre os preços dos dados coletados em um workspace do Log Analytics.

## <a name="data-collected"></a>Dados coletados
A tabela a seguir lista os tipos de dados que você pode configurar para um workspace do Log Analytics coletar de todos os agentes conectados. Confira [O que é monitorado pelo Azure Monitor?](../monitor-reference.md) para obter uma lista de informações, soluções e outras opções que usam o Agente do Log Analytics para coletar outros tipos de dados.

| fonte de dados | Descrição |
| --- | --- |
| [Logs de eventos do Windows](data-sources-windows-events.md) | Informações enviadas ao sistema de registro de evento do Windows. |
| [Syslog](data-sources-syslog.md)                     | Informações enviadas ao sistema de registro de evento do Linux. |
| [Desempenho](data-sources-performance-counters.md)  | Valores numéricos que medem o desempenho de diferentes aspectos do sistema operacional e das cargas de trabalho. |
| [Logs do IIS](data-sources-iis-logs.md)                 | Informações de uso para sites do IIS em execução no sistema operacional convidado. |
| [Logs personalizados](data-sources-custom-logs.md)           | Eventos de arquivos de texto tanto em computadores Windows quanto Linux. |

## <a name="data-destinations"></a>Destinos de dados
O Agente do Log Analytics envia dados para um workspace do Log Analytics no Azure Monitor. O agente do Windows pode ser de hospedagem múltipla a fim de enviar dados para vários espaços de trabalho e grupos de gerenciamento do System Center Operations Manager. O agente do Linux pode enviar apenas para um único destino.

## <a name="other-services"></a>Outros serviços
O agente para Linux e Windows não serve apenas para conectar-se ao Azure Monitor, também oferece suporte à Automação do Azure para hospedar a função de trabalho do Runbook Híbrido e outros serviços, como [Change Tracking](../../automation/change-tracking.md), [Gerenciamento de Atualizações](../../automation/update-management/update-mgmt-overview.md) e [Central de Segurança do Azure](../../security-center/security-center-intro.md). Para obter mais informações sobre a função Hybrid Runbook Worker, consulte [Hybrid Runbook Worker de Automação do Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalação e configuração

Ao usar os Agentes do Log Analytics para coletar dados, você precisará entender o seguinte para planejar a implantação do agente:

* Para coletar dados de agentes do Windows, você poderá [configurar cada agente para relatar a um ou mais espaços de trabalho](agent-windows.md), mesmo que estejam relatando a um grupo de gerenciamento do System Center Operations Manager. O agente do Windows pode relatar a até quatro espaços de trabalho.
* Não há suporte à hospedagem múltipla em um agente do Linux e ele somente pode relatar a um único espaço de trabalho.
* O agente do Windows dá suporte ao [padrão FIPS 140](/windows/security/threat-protection/fips-140-validation); já o agente do Linux não dá suporte a ele.  

Se você estiver usando o System Center Operations Manager 2012 R2 ou posterior:

* cada grupo de gerenciamento do Operations Manager pode ser [conectado a apenas um espaço de trabalho](om-agents.md).
* Os computadores Linux que relatam a um grupo de gerenciamento devem ser configurados para relatar diretamente a um workspace do Log Analytics. Se os computadores Linux já estiverem se reportando diretamente a um espaço de trabalho e você quiser monitorá-los com o Operations Manager, siga estas etapas para [Relatar a um grupo de gerenciamento do Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Você pode instalar o Agente do Windows do Log Analytics no computador Windows e fazer com que ele se reporte tanto ao Operations Manager integrado a um espaço de trabalho quanto a outro espaço de trabalho.


Há vários métodos para instalar o Agente do Log Analytics e conectar seu computador ao Azure Monitor, dependendo dos requisitos. A tabela a seguir realça cada método para determinar o que funciona melhor em sua organização.

|Fonte | Método | Descrição|
|-------|-------------|-------------|
|VM do Azure| [Manualmente no portal do Azure](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json) | Especifique as VMs a serem implantadas pelo workspace do Log Analytics. |
| | Extensão de VM do Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) usando a CLI do Azure ou com um modelo do Azure Resource Manager | A extensão instala o agente do Log Analytics nas máquinas virtuais do Azure e as registra em um workspace do Azure Monitor existente. |
| | [Azure Monitor para VMs](../insights/vminsights-enable-overview.md) | Quando você habilita o monitoramento com o Azure Monitor para VMs, ele instala a extensão e o Agente do Log Analytics. |
| | [Provisionamento automático da Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md) | A Central de Segurança do Azure pode provisionar o Agente do Log Analytics em todas as VMs do Azure com suporte e nas que vierem a ser criadas se você a habilitar para monitorar vulnerabilidades de segurança e ameaças. Se habilitada, as VMs novas ou existentes sem um agente instalado serão provisionadas. |
| Computador Windows híbrido| [Instalação manual](agent-windows.md) | Instale o Microsoft Monitoring Agent na linha de comando. |
| | [DSC de Automação do Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatize a instalação com o DSC de Automação do Azure. |
| | [Modelo do Resource Manager com o Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Use um modelo do Azure Resource Manager se você implantou o Microsoft Azure Stack em seu datacenter.| 
| Computador Linux híbrido| [Instalação manual](../learn/quick-collect-linux-computer.md)|Instale o agente para Linux chamando um script de wrapper hospedado no GitHub. | 
| System Center Operations Manager|[Conectar o Operations Manager ao Log Analytics](./om-agents.md) | Configure a integração entre o Operations Manager e os logs do Azure Monitor para encaminhar dados coletados de computadores Windows subordinados a um grupo de gerenciamento.|  


## <a name="supported-windows-operating-systems"></a>Sistemas operacionais Windows compatíveis

Há suporte oficial para as seguintes versões do sistema operacional Windows para o agente para Windows:

* Windows Server 2019
* Windows Server 2016, versão 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (incluindo multissessão) e Pro
* Windows 8 Enterprise e Pro 
* Windows 7 SP1

>[!NOTE]
>Embora o Agente do Log Analytics para Windows tenha sido projetado para dar suporte a cenários de monitoramento de servidor, percebemos que você pode executar o Windows Client para dar suporte a cargas de trabalho configuradas e otimizadas para o sistema operacional do servidor. O agente dá suporte ao Windows Client, mas nossas soluções de monitoramento se concentram em cenários de monitoramento de clientes somente quando explicitamente declarado.

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

Esta seção fornece detalhes sobre as distribuições de Linux com suporte.

Começando com versões lançadas depois de agosto de 2018, estamos fazendo as seguintes alterações ao nosso modelo de suporte:  

* Somente o servidor versões têm suporte, um não cliente.  
* Concentre o suporte em uma das [Distribuições endossadas do Linux do Azure ](../../virtual-machines/linux/endorsed-distros.md). Observe que pode haver algum atraso entre uma nova distribuição/versão sendo endossada pelo Linux do Azure e seu suporte ao Agente do Log Analytics Linux.
* Todas as versões secundárias têm suporte para cada versão principal listada.
* As versões que passaram a data de fim de suporte do fabricante não são suportadas.  
* Não há suporte para novas versões do AMI.  
* Apenas versões que executam o SSL 1.x por padrão são suportadas.

>[!NOTE]
>Se você estiver usando uma distribuição ou versão que não é suportada no momento e não se alinha ao nosso modelo de suporte, recomendamos que você distribua esse repositório, reconhecendo que o suporte da Microsoft não fornecerá assistência com as versões do agente bifurcado.


### <a name="python-2-requirement"></a>Requisito do Python 2
 O agente de Log Analytics requer o Python 2. Se sua máquina virtual estiver usando um distribuição que não inclua o Python 2 por padrão, você deverá instalá-lo. Os comandos de exemplo a seguir instalarão o Python 2 em distribuições diferentes.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

O executável python2 deve ter um alias para *Python* usando o seguinte procedimento:

1. Execute o comando a seguir para exibir qualquer alias atual do Python, se houver. Se tiver, anote a prioridade para a próxima etapa.
 
    ```
    sudo update-alternatives ––display python
    ```

2. Execute o comando a seguir. Substituir *\<priority\>* por um número maior do que qualquer prioridade de link existente ou 1 se não existir nenhum link no momento.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 <priority>
    ```

### <a name="supported-distros"></a>Distribuições com suporte

As seguintes versões do sistema operacional Linux têm suporte oficial para o agente do Linux:

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64), 7 (x64) e 8 (x64)
* Debian GNU/Linux 8 e 9 (x64)
* Ubuntu 14.04 18.04 LTS (x86/x64), 16.04 LTS (x64) e LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) e 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 só tem suporte em plataformas de x86_x64 (64 bits) e OpenSSL mais cedo do que 1. x não tem suporte em qualquer plataforma.
>

### <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela a seguir realça os pacotes necessários para distribuições do Linux com suporte em que o agente será instalado.

|Pacote necessário |Descrição |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|Openssl    | Bibliotecas OpenSSL | 1.0.x ou 1.1.x |
|Curl | cliente Web cURL | 7.15.5 |
|Python | | 2.6 + ou 3.3 +
|Python-ctypes | | 
|PAM | Módulos de autenticação conectáveis | | 

>[!NOTE]
>Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para os logs do Azure Monitor, incentivamos você a configurar o agente para usar pelo menos o protocolo TLS 1.2. Constatou-se que versões mais antigas do protocolo TLS/protocolo SSL eram vulneráveis e embora elas ainda funcionem no momento para permitir a compatibilidade com versões anteriores, elas **não são recomendadas**.  Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Requisito de suporte à assinatura de código SHA-2 para Windows
O agente do Windows começará a usar exclusivamente a assinatura SHA-2 em 2 de novembro de 2020. Essa alteração afetará os clientes que usam o Agente do Log Analytics em um sistema operacional herdado como parte de um serviço do Azure (Azure Monitor, Automação do Azure, Gerenciamento de Atualizações do Azure, Controle de Alterações do Azure, Central de Segurança do Azure, Azure Sentinel, Windows Defender ATP). A alteração não requer nenhuma ação do cliente, a menos que você esteja executando o agente em uma versão do sistema operacional herdada (Windows 7, Windows Server 2008 R2 e Windows Server 2008). Os clientes em execução em uma versão de sistema operacional herdada são necessários para executar as seguintes ações em suas máquinas antes de 2 de novembro de 2020 ou seus agentes deixarão de enviar dados para seus espaços de trabalho de Log Analytics:

1. Instale o Service Pack mais recente para seu sistema operacional. As versões do service pack necessárias são:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Instale as atualizações do Windows de assinatura do SHA-2 para seu sistema operacional conforme descrito no requisito de suporte à assinatura de código [SHA-2 2019 para Windows e WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Atualize para a versão mais recente do agente do Windows (versão 10.20.18029).
4. É recomendável configurar o agente para [usar o TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Requisitos de rede
O agente para Linux e Windows comunica saída para o serviço Azure Monitor pela porta TCP 443 e, se a máquina se conectar por meio de um firewall ou servidor proxy para se comunicar pela Internet, revise os requisitos abaixo para o reconhecimento da configuração de rede necessária. Se as políticas de segurança de TI não permitirem que os computadores na rede se conectem à Internet, você poderá configurar um [gateway do Log Analytics](gateway.md) e configurar o agente para se conectar por meio do gateway aos logs do Azure Monitor. O agente pode receber informações de configuração e enviar dados coletados, dependendo de quais regras de coleta de dados e soluções de monitoramento você ativou em seu workspace.

![Diagrama de comunicação do agente do Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

A tabela a seguir lista as informações de configuração de proxy e firewall necessárias para que os agentes do Linux e do Windows se comuniquem com os logs do Azure Monitor.

### <a name="firewall-requirements"></a>Requisitos de firewall

|Recurso de agente|Portas |Direção |Ignorar a inspeção de HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Saída|Sim |
|*.azure-automation.net |Porta 443 |Saída|Sim |

Para obter informações de firewall necessárias para o Azure Governamental, confira [Gerenciamento do Azure Governamental](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Se você planeja usar o Hybrid Runbook Worker da Automação do Azure para conectar e se registrar no serviço de automação para usar runbooks ou soluções de gerenciamento em seu ambiente, é necessário ter acesso ao número da porta e as URLs descritas em [Configurar sua rede para o Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Configuração de Proxy

O agente do Windows e do Linux é compatível com a comunicação por meio de um servidor proxy ou de um gateway do Log Analytics para o Azure Monitor usando o protocolo HTTPS.  Há suporte para a autenticação anônima e básica (nome de usuário/senha).  Para o agente do Windows conectado diretamente ao serviço, a configuração do proxy é especificada durante a instalação ou [após a implementação](agent-manage.md#update-proxy-settings) no Painel de Controle ou no PowerShell.  

Para o agente Linux, o servidor proxy pode ser especificado durante a instalação ou modificando o arquivo de configuração proxy.conf [após a instalação](agent-manage.md#update-proxy-settings).  O valor de configuração de proxy do agente do Linux tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | HTTPS |
|usuário | Nome de usuário opcional para autenticação de proxy |
|password | Senha opcional para autenticação de proxy |
|proxyhost | Endereço ou FQDN do servidor proxy/gateway do Log Analytics |
|porta | Número da porta opcional para o servidor proxy/gateway do Log Analytics |

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se você usar caracteres especiais, como “\@” em sua senha, receberá um erro de conexão de proxy porque o valor será analisado incorretamente.  Para contornar esse problema, codifique a senha na URL usando uma ferramenta como [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Próximas etapas

* Consultar as [fontes de dados](agent-data-sources.md) para entender as fontes de dados disponíveis para coletar dados do sistema Windows ou Linux. 
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 
* Conheça as [soluções de monitoramento](../insights/solutions.md) que adicionam funcionalidade ao Azure Monitor e também coletam dados no espaço de trabalho do Log Analytics.

