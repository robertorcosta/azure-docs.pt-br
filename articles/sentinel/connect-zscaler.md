---
title: Conecte os dados do Zscaler ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados do Zscaler ao Azure Sentinel.
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
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587984"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Conecte o acesso à Internet do Zscaler ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Zscaler no Azure Sentinel está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu aparelho Zscaler Internet Access ao Azure Sentinel. O conector de dados Zscaler permite conectar facilmente seus logs de Acesso à Internet (ZIA) zscaler com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. O uso do Zscaler no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e melhorará seus recursos de operação de segurança. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configure seu Zscaler para enviar mensagens CEF

1. No aparelho Zscaler você precisa definir esses valores para que o aparelho envie os registros necessários no formato necessário para o agente Azure Sentinel Syslog, com base no agente Log Analytics. Você pode modificar esses parâmetros em seu aparelho, desde que você também modifique-os no daemon Syslog no agente Azure Sentinel.
    - Protocolo = TCP
    - Porto = 514
    - Formato = CEF
    - Endereço IP - certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que você dedicou para este fim.
 Para obter mais informações, consulte o Guia de Implantação do [Zscaler e do Sentinel Azure](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Esta solução suporta Syslog RFC 3164 ou RFC 5424.


1. Para usar o esquema relevante no Log Analytics para `CommonSecurityLog`os eventos da CEF, procure .
1. Continue até [o PASSO 3: Valide a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Zscaler Internet Access ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.


