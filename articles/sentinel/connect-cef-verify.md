---
title: Validar a conectividade com o Azure Sentinel | Microsoft Docs
description: Valide a conectividade da solução de segurança para garantir que as mensagens CEF sejam encaminhadas para o Azure Sentinel.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731825"
---
# <a name="step-3-validate-connectivity"></a>ETAPA 3: validar a conectividade

Depois de implantar o encaminhador de log (na etapa 1) e configurar sua solução de segurança para enviar mensagens de CEF de ti (na etapa 2), siga estas instruções para verificar a conectividade entre sua solução de segurança e o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões elevadas (sudo) em seu computador do encaminhador de log.

- Você deve ter o Python instalado no computador do encaminhador de log.<br>
Use o `python –version` comando para verificar.

## <a name="how-to-validate-connectivity"></a>Como validar a conectividade

1. No menu de navegação do Azure Sentinel, abra **logs**. Execute uma consulta usando o esquema **CommonSecurityLog** para ver se você está recebendo logs de sua solução de segurança.<br>
Lembre-se de que pode levar cerca de 20 minutos até que os logs comecem a aparecer na **log Analytics**. 

1. Se você não vir nenhum resultado da consulta, verifique se os eventos estão sendo gerados de sua solução de segurança ou tente gerar alguns e verifique se eles estão sendo encaminhados para o computador do Encaminhador do syslog que você designou. 

1. Execute o script a seguir no encaminhador de log para verificar a conectividade entre sua solução de segurança, o encaminhador de log e o Azure Sentinel. Esse script verifica se o daemon está escutando nas portas corretas, se o encaminhamento está configurado corretamente e se nada está bloqueando a comunicação entre o daemon e o agente de Log Analytics. Ele também envia as mensagens de simulação ' TestCommonEventFormat ' para verificar a conectividade de ponta a ponta. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Script de validação explicado

O script de validação executa as seguintes verificações:

# <a name="rsyslog-daemon"></a>[daemon do rsyslog](#tab/rsyslog)

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

1. Verifica se existem aprimoramentos de segurança no computador que podem estar bloqueando o tráfego de rede (como um firewall de host).

1. Verifica se o daemon do syslog (rsyslog) está configurado corretamente para enviar mensagens que ele identifica como CEF (usando um Regex) para o agente de Log Analytics na porta TCP 25226:

    - Arquivo de configuração:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Verifica se o daemon do syslog está recebendo dados na porta 514

1. Verifica se as conexões necessárias são estabelecidas: TCP 514 para receber dados, TCP 25226 para comunicação interna entre o daemon de syslog e o agente de Log Analytics

1. Envia dados FICTÍCIOs para a porta 514 no localhost. Esses dados devem ser observáveis no espaço de trabalho do Azure Sentinel executando a seguinte consulta:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[daemon do syslog-ng](#tab/syslogng)

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

1. Verifica se existem aprimoramentos de segurança no computador que podem estar bloqueando o tráfego de rede (como um firewall de host).

1. Verifica se o daemon de syslog (syslog-ng) está configurado corretamente para enviar mensagens que ele identifica como CEF (usando um Regex) para o agente de Log Analytics na porta TCP 25226:

    - Arquivo de configuração:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Verifica se o daemon do syslog está recebendo dados na porta 514

1. Verifica se as conexões necessárias são estabelecidas: TCP 514 para receber dados, TCP 25226 para comunicação interna entre o daemon de syslog e o agente de Log Analytics

1. Envia dados FICTÍCIOs para a porta 514 no localhost. Esses dados devem ser observáveis no espaço de trabalho do Azure Sentinel executando a seguinte consulta:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar os dispositivos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

