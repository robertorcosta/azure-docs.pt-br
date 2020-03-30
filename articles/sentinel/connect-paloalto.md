---
title: Conecte os dados da Palo Alto Networks ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar os dados da Palo Alto Networks ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588120"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Conecte as redes de Palo Alto ao Azure Sentinel



Este artigo explica como conectar seu aparelho Palo Alto Networks ao Azure Sentinel. O conector de dados Palo Alto Networks permite conectar facilmente seus registros de Redes Palo Alto com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. O uso do Palo Alto Networks no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e melhorará seus recursos de operação de segurança. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Forward Palo Alto Networks logs para o agente Syslog

Configure as Redes Palo Alto para encaminhar mensagens Syslog no formato CEF para o seu espaço de trabalho Azure através do agente Syslog:
1.  Acesse [as guias de configuração do Common Event Format (CEF)](https://docs.paloaltonetworks.com/resources/cef) e baixe o pdf para o seu tipo de aparelho. Siga todas as instruções no guia para configurar o seu aparelho Palo Alto Networks para coletar eventos da CEF. 

1.  Vá para [Configurar o monitoramento do Syslog](https://aka.ms/asi-syslog-paloalto-forwarding) e siga as etapas 2 e 3 para configurar o encaminhamento de eventos cef do seu aparelho Palo Alto Networks para o Azure Sentinel.

    1. Certifique-se de definir o formato do **servidor Syslog** como **BSD**.

       > [!NOTE]
       > As operações de cópia/cola do PDF podem alterar o texto e inserir caracteres aleatórios. Para evitar isso, copie o texto para um editor e remova quaisquer caracteres que possam quebrar o formato de log antes de copiá-lo, como você pode ver neste exemplo.
 
        ![Problema de cópia de texto da CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Para usar o esquema relevante no Log Analytics para os eventos Palo Alto Networks, procure **o CommonSecurityLog**.

1. Continue até [o PASSO 3: Valide a conectividade](connect-cef-verify.md).




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar os aparelhos da Palo Alto Networks ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.


