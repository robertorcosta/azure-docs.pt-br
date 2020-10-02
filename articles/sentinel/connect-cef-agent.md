---
title: Implantar o encaminhador de log para conectar dados de CEF ao Azure Sentinel | Microsoft Docs
description: Saiba como implantar o agente para conectar dados de CEF ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: a54dfa0f2b072d30cac605937a1b623ef9d4051d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631487"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Etapa 1: implantar o encaminhador de log


Nesta etapa, você designará e configurará o computador Linux que encaminhará os logs de sua solução de segurança para seu espaço de trabalho do Azure Sentinel. Esse computador pode ser uma máquina física ou virtual em seu ambiente local, uma VM do Azure ou uma VM em outra nuvem. Usando o link fornecido, você executará um script no computador designado que executa as seguintes tarefas:
- Instala o agente do Log Analytics para Linux (também conhecido como agente do OMS) e o configura para as seguintes finalidades:
    - escutando mensagens de CEF do daemon de syslog do Linux interno na porta TCP 25226
    - enviando as mensagens com segurança por TLS para seu espaço de trabalho do Azure Sentinel, onde elas são analisadas e aprimoradas

- Configura o daemon de syslog do Linux interno (rsyslog. d/syslog-ng) para as seguintes finalidades:
    - escutando mensagens de syslog de suas soluções de segurança na porta TCP 514
    - encaminhar somente as mensagens que ele identifica como CEF para o agente de Log Analytics no localhost usando a porta TCP 25226
 
## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões elevadas (sudo) em seu computador Linux designado.
- Você deve ter o Python instalado no computador Linux.<br>Use o `python -version` comando para verificar.
- O computador Linux não deve estar conectado a nenhum espaço de trabalho do Azure antes de instalar o agente de Log Analytics.

## <a name="run-the-deployment-script"></a>Executar o script de implantação
 
1. No menu de navegação do Azure Sentinel, clique em **conectores de dados**. Na lista de conectores, clique no bloco **CEF (formato de evento comum)** e, em seguida, no botão **abrir página do conector** no canto inferior direito. 

1. Em **1,2 Instale o coletor CEF no computador Linux**, copie o link fornecido em **executar o script a seguir para instalar e aplicar o coletor CEF**ou do texto abaixo:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Enquanto o script estiver em execução, verifique se você não recebe mensagens de erro ou de aviso.

> [!NOTE]
> **Usando o mesmo computador para encaminhar mensagens de syslog *e* CEF simples**
>
> Se você planeja usar esse computador do encaminhador de logs para encaminhar [mensagens de syslog](connect-syslog.md) , bem como CEF, para evitar a duplicação de eventos para as tabelas syslog e CommonSecurityLog:
>
> 1. Em cada computador de origem que envia logs para o encaminhador no formato CEF, você deve editar o arquivo de configuração do syslog para remover as instalações que estão sendo usadas para enviar mensagens CEF. Dessa forma, os recursos enviados no CEF também não serão enviados no syslog. Consulte [Configurar syslog no agente do Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) para obter instruções detalhadas sobre como fazer isso.
>
> 1. Você deve executar o comando a seguir nessas máquinas para desabilitar a sincronização do agente com a configuração de syslog no Azure Sentinel. Isso garante que a alteração de configuração feita na etapa anterior não seja substituída.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

Continue na [etapa 2: configurar sua solução de segurança para encaminhar mensagens CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Script de implantação explicado

A seguir está uma descrição de comando por comando das ações do script de implantação.

Escolha um daemon syslog para ver a descrição apropriada.

# <a name="rsyslog-daemon"></a>[daemon do rsyslog](#tab/rsyslog)

1. **Baixando e instalando o agente de Log Analytics:**

    - Baixa o script de instalação para o agente do Log Analytics (OMS) do Linux.

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Instala o agente de Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Definindo a configuração do agente de Log Analytics para escutar na porta 25226 e encaminhar mensagens CEF para o Azure Sentinel:**

    - Baixa a configuração do repositório GitHub do agente de Log Analytics.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Configurando o daemon do syslog:**

    - Abre a porta 514 para a comunicação TCP usando o arquivo de configuração do syslog `/etc/rsyslog.conf` .

    - Configura o daemon para encaminhar mensagens CEF para o agente de Log Analytics na porta TCP 25226, inserindo um arquivo de configuração especial `security-config-omsagent.conf` no diretório do daemon do syslog `/etc/rsyslog.d/` .

        Conteúdo do `security-config-omsagent.conf` arquivo:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Reiniciando o daemon do syslog e o agente de Log Analytics:**

    - Reinicia o daemon rsyslog.
    
        ```bash
        service rsyslog restart
        ```

    - Reinicia o agente de Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Verificando o mapeamento do campo *computador* conforme esperado:**

    - Garante que o campo *computador* na origem do syslog esteja corretamente mapeado no agente de log Analytics executando esse comando e reiniciando o agente.

        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
            -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
            filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[daemon do syslog-ng](#tab/syslogng)

1. **Baixando e instalando o agente de Log Analytics:**

    - Baixa o script de instalação para o agente do Log Analytics (OMS) do Linux.

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Instala o agente de Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Definindo a configuração do agente de Log Analytics para escutar na porta 25226 e encaminhar mensagens CEF para o Azure Sentinel:**

    - Baixa a configuração do repositório GitHub do agente de Log Analytics.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Configurando o daemon do syslog:**

    - Abre a porta 514 para a comunicação TCP usando o arquivo de configuração do syslog `/etc/syslog-ng/syslog-ng.conf` .

    - Configura o daemon para encaminhar mensagens CEF para o agente de Log Analytics na porta TCP 25226, inserindo um arquivo de configuração especial `security-config-omsagent.conf` no diretório do daemon do syslog `/etc/syslog-ng/conf.d/` .

        Conteúdo do `security-config-omsagent.conf` arquivo:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Reiniciando o daemon do syslog e o agente de Log Analytics:**

    - Reinicia o daemon syslog-ng.
    
        ```bash
        service syslog-ng restart
        ```

    - Reinicia o agente de Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Verificando o mapeamento do campo *computador* conforme esperado:**

    - Garante que o campo *computador* na origem do syslog esteja corretamente mapeado no agente de log Analytics executando esse comando e reiniciando o agente.

        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
            -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
            filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a implantar o agente de Log Analytics para conectar dispositivos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

