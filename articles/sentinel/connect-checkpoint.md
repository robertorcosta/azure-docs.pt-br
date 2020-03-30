---
title: Conecte os dados do Check Point ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados do Check Point ao Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588409"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Conecte o ponto de verificação ao Azure Sentinel



Este artigo explica como conectar seu aparelho Check Point ao Azure Sentinel. O conector de dados check point permite conectar facilmente seus registros do Check Point com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. O uso do Check Point no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e melhorará seus recursos de operação de segurança. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Logs de ponto de verificação para o agente Syslog

Configure seu aparelho Check Point para encaminhar mensagens Syslog no formato CEF para o espaço de trabalho do Azure através do agente Syslog.

1. Vá para [Verificar a exportação de log de ponto](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Desça até a **Implantação Básica** e siga as instruções para configurar a conexão, usando as seguintes diretrizes:
   - Defina a **porta Syslog** para **514** ou a porta que você definiu no agente.
     - Substitua o **nome** e o **endereço IP do servidor de destino** na CLI pelo nome do agente Syslog e endereço IP.
     - Defina o formato como **CEF**.
1. Se estiver usando a versão R77.30 ou R80.10, role até **instalações** e siga as instruções para instalar um Log Exporter para sua versão.
1. Continue até [o PASSO 3: Valide a conectividade](connect-cef-verify.md).
 

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar os aparelhos Check Point ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- [Validar a conectividade](connect-cef-verify.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.


