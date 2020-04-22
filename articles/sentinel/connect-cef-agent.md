---
title: Implantar o encaminhador de log para conectar dados do CEF ao Azure Sentinel | Microsoft Docs
description: Saiba como implantar o agente para conectar dados do CEF ao Azure Sentinel.
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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731654"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Passo 1: Implante o encaminhador de log


Nesta etapa, você designará e configurará a máquina Linux que encaminhará os logs da sua solução de segurança para o espaço de trabalho do Azure Sentinel. Esta máquina pode ser uma máquina física ou virtual em seu ambiente local, uma VM Azure ou uma VM em outra nuvem. Usando o link fornecido, você executará um script na máquina designada que executa as seguintes tarefas:
- Instala o agente Log Analytics para Linux (também conhecido como agente OMS) e o configura para os seguintes propósitos:
    - ouvir as mensagens CEF do daemon Linux Syslog incorporado na porta TCP 25226
    - enviando as mensagens com segurança sobre O TLS para o seu espaço de trabalho Do Azure Sentinel, onde eles são analisados e enriquecidos

- Configura o daemon Linux Syslog incorporado (rsyslog.d/syslog-ng) para os seguintes propósitos:
    - ouvir mensagens syslog de suas soluções de segurança na porta TCP 514
    - encaminhando apenas as mensagens que identifica como CEF para o agente Log Analytics no localhost usando a porta TCP 25226
 
## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões elevadas (sudo) em sua máquina Linux designada.
- Você deve ter python instalado na máquina Linux.<br>Use `python -version` o comando para verificar.
- A máquina Linux não deve ser conectada a nenhum espaço de trabalho do Azure antes de instalar o agente Log Analytics.

## <a name="run-the-deployment-script"></a>Executar o script de implantação
 
1. No menu de navegação do Azure Sentinel, clique em **conectores de dados**. Na lista de conectores, clique no azulejo **Common Event Format (CEF)** e, em seguida, no botão **de página do conector Aberto** no canto inferior direito. 

1. Em **1.2 Instale o coletor CEF na máquina Linux,** copie o link fornecido em **Execute o seguinte script para instalar e aplicar o coletor CEF**, ou a partir do texto abaixo:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Enquanto o script estiver em execução, verifique se você não recebe nenhum erro ou mensagens de aviso.

Continue até [o PASSO 2: Configure sua solução de segurança para encaminhar mensagens CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Script de implantação explicado

A seguir está uma descrição de comando por comando das ações do script de implantação.

Escolha um daemon syslog para ver a descrição apropriada.

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. **Baixando e instalando o agente Log Analytics:**

    - Baixa o script de instalação para o agente Linux do Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instala o agente Log Analytics<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurando o daemon Syslog:**

    1. Abre a porta 514 para comunicação TCP `/etc/rsyslog.conf`usando o arquivo de configuração syslog .

    1. Configura o daemon para encaminhar mensagens CEF para o agente Log Analytics na porta TCP `security-config-omsagent.conf` 25226, inserindo um arquivo de configuração especial no diretório `/etc/rsyslog.d/`do daemon syslog .

        Conteúdo do `security-config-omsagent.conf` arquivo:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **Reiniciando o daemon Syslog**

    `service rsyslog restart`

1. **Configurando a configuração do agente Log Analytics para ouvir na porta 25226 e encaminhar mensagens CEF para o Azure Sentinel**

    1. Baixa a configuração do repositório gitHub do agente Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Reinicializa o agente log analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **Baixando e instalando o agente Log Analytics:**

    - Baixa o script de instalação para o agente Linux do Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instala o agente Log Analytics<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurando o daemon Syslog:**

    1. Abre a porta 514 para comunicação TCP `/etc/syslog-ng/syslog-ng.conf`usando o arquivo de configuração syslog .

    1. Configura o daemon para encaminhar mensagens CEF para o agente Log Analytics na porta TCP `security-config-omsagent.conf` 25226, inserindo um arquivo de configuração especial no diretório `/etc/syslog-ng/conf.d/`do daemon syslog .

        Conteúdo do `security-config-omsagent.conf` arquivo:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Reiniciando o daemon Syslog**

    `service syslog-ng restart`

1. **Configurando a configuração do agente Log Analytics para ouvir na porta 25226 e encaminhar mensagens CEF para o Azure Sentinel**

    1. Baixa a configuração do repositório gitHub do agente Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Reinicializa o agente log analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como implantar o agente Log Analytics para conectar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

