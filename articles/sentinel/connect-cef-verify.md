---
title: Validar a conectividade com o Azure Sentinel | Microsoft Docs
description: Valide a conectividade de sua solução de segurança para garantir que as mensagens CEF estejam sendo encaminhadas para o Azure Sentinel.
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
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731825"
---
# <a name="step-3-validate-connectivity"></a>PASSO 3: Validar a conectividade

Depois de implantar o seu encaminhador de log (na Etapa 1) e configurar sua solução de segurança para enviá-lo mensagens CEF (na Etapa 2), siga estas instruções para verificar a conectividade entre sua solução de segurança e o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões elevadas (sudo) na máquina de encaminhamento de log.

- Você deve ter python instalado em sua máquina de encaminhamento de log.<br>
Use `python –version` o comando para verificar.

## <a name="how-to-validate-connectivity"></a>Como validar a conectividade

1. No menu de navegação do Azure Sentinel, abra **Logs**. Execute uma consulta usando o esquema **CommonSecurityLog** para ver se você está recebendo logs de sua solução de segurança.<br>
Esteja ciente de que pode levar cerca de 20 minutos até que seus registros comecem a aparecer no **Log Analytics**. 

1. Se você não ver nenhum resultado da consulta, verifique se os eventos estão sendo gerados a partir de sua solução de segurança ou tente gerar alguns, e verifique se eles estão sendo encaminhados para a máquina de encaminhamento Syslog que você designou. 

1. Execute o script a seguir no encaminhador de log para verificar a conectividade entre sua solução de segurança, o encaminhador de log e o Azure Sentinel. Este script verifica se o daemon está ouvindo nas portas corretas, que o encaminhamento está configurado corretamente e que nada está bloqueando a comunicação entre o daemon e o agente do Log Analytics. Ele também envia mensagens falsas 'TestCommonEventFormat' para verificar a conectividade de ponta a ponta. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Script de validação explicado

O script de validação executa as seguintes verificações:

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. Verifica se o arquivo<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe e é válido.

1. Verifica se o arquivo inclui o seguinte texto:

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. Verifica se há algum aprimoramento de segurança na máquina que possa estar bloqueando o tráfego da rede (como um firewall host).

1. Verifica se o daemon syslog (rsyslog) está configurado corretamente para enviar mensagens que identifica como CEF (usando um regex) para o agente Log Analytics na porta TCP 25226:

    - Arquivo de configuração:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Verifica se o daemon syslog está recebendo dados na porta 514

1. Verifica se as conexões necessárias estão estabelecidas: tcp 514 para recebimento de dados, tcp 25226 para comunicação interna entre o daemon syslog e o agente log analytics

1. Envia dados MOCK para a porta 514 no localhost. Esses dados devem ser observáveis no espaço de trabalho do Azure Sentinel executando a seguinte consulta:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. Verifica se o arquivo<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe e é válido.

1. Verifica se o arquivo inclui o seguinte texto:

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. Verifica se há algum aprimoramento de segurança na máquina que possa estar bloqueando o tráfego da rede (como um firewall host).

1. Verifica se o daemon syslog (syslog-ng) está configurado corretamente para enviar mensagens que identifica como CEF (usando um regex) para o agente Log Analytics na porta TCP 25226:

    - Arquivo de configuração:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Verifica se o daemon syslog está recebendo dados na porta 514

1. Verifica se as conexões necessárias estão estabelecidas: tcp 514 para recebimento de dados, tcp 25226 para comunicação interna entre o daemon syslog e o agente log analytics

1. Envia dados MOCK para a porta 514 no localhost. Esses dados devem ser observáveis no espaço de trabalho do Azure Sentinel executando a seguinte consulta:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.

