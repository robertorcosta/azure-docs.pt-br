---
title: Conecte os dados do Fortinet ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados do Fortinet ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588188"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Conecte fortinet ao Azure Sentinel



Este artigo explica como conectar seu aparelho Fortinet ao Azure Sentinel. O conector de dados Fortinet permite conectar facilmente seus registros do Fortinet com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. O uso do Fortinet no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e melhorará seus recursos de operação de segurança. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Avançar registros fortinet para o agente Syslog

Configure o Fortinet para encaminhar mensagens Syslog no formato CEF para o espaço de trabalho do Azure através do agente Syslog.

1. Abra o CLI no seu aparelho Fortinet e execute os seguintes comandos:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Substitua o **endereço IP** do servidor pelo endereço IP do agente.
    - Defina a **porta syslog** para **514** ou o conjunto de porta no agente.
    - Para ativar o formato CEF nas primeiras versões do FortiOS, talvez seja necessário executar o conjunto de **comandos csv desabilitado**.
 
   > [!NOTE] 
   > Para obter mais informações, vá para a [biblioteca de documentos Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Selecione sua versão e use o **Manual e** a Referência **de Mensagens de Registro**.

1. Para usar o esquema relevante no Azure Monitor Log Analytics para `CommonSecurityLog`os eventos fortinet, procure .

1. Continue até [o PASSO 3: Valide a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como conectar os aparelhos Fortinet ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.


