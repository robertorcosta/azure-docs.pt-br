---
title: Conecte os dados da Cisco ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados cisco ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588392"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Conecte o Cisco ASA ao Azure Sentinel



Este artigo explica como conectar seu aparelho Cisco ASA ao Azure Sentinel. O conector de dados Cisco ASA permite conectar facilmente seus logs Cisco ASA com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. O uso do Cisco ASA no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e melhorará seus recursos de operação de segurança. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Avançar registros Cisco ASA para o agente Syslog

O Cisco ASA não suporta cef, então os logs são enviados como Syslog e o agente do Azure Sentinel sabe como analisá-los como se fossem logs do CEF. Configure o Cisco ASA para encaminhar mensagens Syslog para o seu espaço de trabalho do Azure através do agente Syslog:

1. Vá para [Enviar mensagens syslog para um servidor Syslog externo](https://aka.ms/asi-syslog-cisco-forwarding)e siga as instruções para configurar a conexão. Use esses parâmetros quando solicitado:
    - Defina a **porta** para 514 ou a porta que você definiu no agente.
    - Defina **syslog_ip** no endereço IP do agente.

1. Para usar o esquema relevante no Log Analytics para `CommonSecurityLog`os eventos cisco, procure .

1. Continue até [o PASSO 3: Valide a conectividade](connect-cef-verify.md).




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar os aparelhos Cisco ASA ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.


