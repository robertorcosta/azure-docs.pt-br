---
title: Conecte computadores Linux ao Azure Monitor | Microsoft Docs
description: Este artigo descreve como conectar computadores Linux hospedados em outras nuvens ou no local ao Azure Monitor com o agente Log Analytics para Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 40c279a4beee9fbebe2de7f272fe51d9039f071c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668699"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Conecte computadores Linux ao Azure Monitor

Para monitorar e gerenciar máquinas virtuais ou computadores físicos em seu data center local ou outro ambiente em nuvem com o Azure Monitor, você precisa implantar o agente log analytics e configurá-lo para reportar a um espaço de trabalho do Log Analytics. Adicionalmente, o agente fornece suporte à função do Hybrid Runbook Worker para a Automação do Azure.

O agente Log Analytics para Linux pode ser instalado usando um dos seguintes métodos. Detalhes sobre como usar cada método são fornecidos posteriormente neste artigo.

* [Baixe e instale manualmente](#install-the-agent-manually) o agente. Isso é necessário quando o computador Linux não tem acesso à Internet e estará se comunicando com o Azure Monitor ou a Azure Automation através do [gateway Log Analytics](gateway.md). 
* [Instale o agente para Linux usando um script de invólucro](#install-the-agent-using-wrapper-script) hospedado no GitHub. Este é o método recomendado para instalar e atualizar o agente quando o computador tiver conectividade com a Internet, diretamente ou através de um servidor proxy.

Para entender a configuração com suporte, revise [suporte para sistemas operacionais Linux](log-analytics-agent.md#supported-linux-operating-systems) e [configuração de firewall de rede](log-analytics-agent.md#network-firewall-requirements).

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para se reportar a mais de um espaço de trabalho do Log Analytics. Ele só pode ser configurado para reportar a um grupo de gerenciamento do System Center Operations Manager e ao espaço de trabalho log analytics simultaneamente, ou a qualquer um individual.

## <a name="agent-install-package"></a>Pacote de instalação do agente

O agente Log Analytics para Linux é composto de vários pacotes. O arquivo de versão contém os seguintes pacotes, `--extract` que estão disponíveis executando o feixe shell com o parâmetro:

**Pacote** | **Versão** | **Descrição**
----------- | ----------- | --------------
omsagent | 1.12.15 | O Agente de Análise de Log para Linux
omsconfig | 1.1.1 | Agente de configuração para o agente Log Analytics
omi | 1.6.3 | Open Management Infrastructure (OMI) -- um leve Servidor CIM. *Note que o OMI requer acesso raiz para executar um trabalho cron necessário para o funcionamento do serviço*
scx | 1.6.3 | Provedores de CIM OMI para métricas de desempenho do sistema operacional
apache-cimprov | 1.0.1 | Provedor de monitoramento de desempenho do Servidor HTTP Apache para OMI. Instalado somente se o Servidor HTTP Apache for detectado.
mysql-cimprov | 1.0.1 | Provedor de monitoramento de desempenho do Servidor MySQL para OMI. Instalado somente se o servidor MySQL/MariaDB for detectado.
docker-cimprov | 1.0.0 | Provedor do Docker para OMI. Instalado somente se o Docker for detectado.

### <a name="agent-installation-details"></a>Detalhes da instalação do agente

Depois de instalar o agente Log Analytics para pacotes Linux, são aplicadas as seguintes alterações adicionais de configuração em todo o sistema. Esses artefatos serão removidos quando o pacote omsagent for desinstalado.

* Um usuário sem privilégios chamado: `omsagent` é criado. O daemon corre esta credencial. 
* Um sudoers *incluem* arquivo `/etc/sudoers.d/omsagent`é criado em . Isso autoriza `omsagent` a reinicialização dos daemons syslog e omsagent. Se sudo *incluir* diretivas não forem suportadas na versão instalada do sudo, essas entradas serão escritas para `/etc/sudoers`.
* A configuração de syslog é modificada para encaminhar um subconjunto de eventos para o agente. Para obter mais informações, consulte [Configurar a coleta de dados do Syslog](data-sources-syslog.md).

Em um computador Linux monitorado, o `omsagent`agente é listado como . `omsconfig`é o agente de configuração Log Analytics para Linux que procura uma nova configuração do lado do portal a cada 5 minutos. A configuração nova e atualizada é aplicada aos `/etc/opt/microsoft/omsagent/conf/omsagent.conf`arquivos de configuração do agente localizados em .

## <a name="obtain-workspace-id-and-key"></a>Obter a ID do workspace e a chave

Antes de instalar o agente do Log Analytics para Linux, você precisa da ID do espaço de trabalho e da chave para o espaço de trabalho do Log Analytics. Essas informações são necessárias durante a configuração do agente para configurá-la adequadamente e garantir que ela possa se comunicar com sucesso com o Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. No canto superior esquerdo do portal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, insira **Log Analytics**. A lista filtra com base em sua entrada, à medida que você digita. Selecione **espaços de trabalho do Log Analytics**.

2. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho criado anteriormente. Você deve ter atribuído a ele o nome de **DefaultLAWorkspace**.

3. Selecione **configurações avançadas:**

    ![Menu Configurações Avançadas do Log Analytics no portal do Azure](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selecione **Fontes Conectadas** e, em seguida, selecione **Servidores Linux**.

5. O valor à direita da **ID do Workspace** e **Chave Primária**. Copie e cole os dois em seu editor favorito.

## <a name="install-the-agent-manually"></a>Instalar o agente manualmente

O agente Log Analytics para Linux é fornecido em um pacote de script shell auto-extraindo e instalado. Este pacote contém pacotes Debian e RPM para cada um dos componentes do agente e pode ser instalado diretamente ou extraído para recuperar os pacotes individuais. Um pacote é fornecido para x64 e um para arquiteturas x86. 

> [!NOTE]
> Para As VMs do Azure, recomendamos que você instale o agente neles usando a [extensão VM do Azure Log Analytics](../../virtual-machines/extensions/oms-linux.md) para Linux. 

1. [Baixe](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) e transfira o pacote apropriado (x64 ou x86) para o seu VM Linux ou computador físico, usando scp/sftp.

2. Instale o pacote `--install` usando o argumento. Para embarcar em um espaço de trabalho do `-w <WorkspaceID>` `-s <workspaceKey>` Log Analytics durante a instalação, forneça os parâmetros copiados anteriormente.

    >[!NOTE]
    >Você precisa usar `--upgrade` o argumento se quaisquer pacotes dependentes como omi, scx, omsconfig ou suas versões mais antigas forem instalados, como seria o caso se o agente do System Center Operations Manager para Linux já estiver instalado. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Para configurar o agente Linux para instalar e conectar-se a um espaço de trabalho do Log Analytics através de um gateway log analytics, execute o seguinte comando fornecendo os parâmetros de chave proxy, workspace e workspace. Esta configuração pode ser especificada `-p [protocol://][user:password@]proxyhost[:port]`na linha de comando incluindo . A propriedade *proxyhost* aceita um nome de domínio totalmente qualificado ou endereço IP do servidor gateway do Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Se a autenticação for necessária, você precisa especificar o nome de usuário e a senha. Por exemplo:  
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Para configurar o computador Linux para se conectar a um espaço de trabalho do Log Analytics na nuvem do Governo Azure, execute o seguinte comando fornecendo o ID do espaço de trabalho e a chave principal copiada anteriormente.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Se você quiser instalar os pacotes do agente e configurá-lo para reportar a um espaço de trabalho específico do Log Analytics posteriormente, execute o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Se você quiser extrair os pacotes de agente do pacote sem instalar o agente, execute o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Instale o agente usando o script de invólucro

As etapas a seguir configuram a configuração do agente para log analytics na nuvem do Governo Azure e Azure usando o script de wrapper para computadores Linux que podem se comunicar diretamente ou através de um servidor proxy para baixar o agente hospedado no GitHub e instalar o agente.  

Se o computador Linux precisar se comunicar através de um servidor proxy para o `-p [protocol://][user:password@]proxyhost[:port]`Log Analytics, essa configuração pode ser especificada na linha de comando incluindo . A propriedade de `http` `https` *protocolo* aceita ou , e a propriedade *proxyhost* aceita um nome de domínio totalmente qualificado ou endereço IP do servidor proxy. 

Por exemplo: `https://proxy01.contoso.com:30443`

Se a autenticação for necessária em ambos os casos, você precisa especificar o nome de usuário e a senha. Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador Linux para se conectar a um espaço de trabalho do Log Analytics, execute o seguinte comando que forneça o ID do espaço de trabalho e a chave principal. O comando a seguir baixa o agente, valida sua soma de verificação e o instala.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    O seguinte comando inclui o parâmetro de proxy `-p` e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador Linux para se conectar ao workspace do Log Analytics na nuvem do Azure Governamental, execute o seguinte comando fornecendo a ID do workspace e a chave primária copiadas anteriormente. O comando a seguir baixa o agente, valida sua soma de verificação e o instala. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    O seguinte comando inclui o parâmetro de proxy `-p` e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Reinicie o agente executando o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Atualizar de uma versão anterior

A atualização de uma versão anterior, começando pela versão 1.0.0-47, é suportada em cada versão. Execute a instalação `--upgrade` com o parâmetro para atualizar todos os componentes do agente para a versão mais recente.

## <a name="next-steps"></a>Próximas etapas

- Revise [o gerenciamento e a manutenção do agente Log Analytics para Windows e Linux](agent-manage.md) para saber como reconfigurar, atualizar ou remover o agente da máquina virtual.

- Consulte [Solução de problemas do agente do Linux](agent-linux-troubleshoot.md) se você encontrar problemas ao instalar ou gerenciar o agente.
