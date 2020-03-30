---
title: Configure sua solução de segurança para conectar dados do CEF ao Azure Sentinel Preview| Microsoft Docs
description: Saiba como configurar sua solução de segurança para conectar dados do CEF ao Azure Sentinel.
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
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588443"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>PASSO 2: Configure sua solução de segurança para enviar mensagens CEF

Nesta etapa, você executará as alterações de configuração necessárias em sua própria solução de segurança para enviar logs para o agente CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configure uma solução com um conector

Se a sua solução de segurança já tiver um conector existente, use as instruções específicas do conector da seguinte forma:

- [Ponto de Verificação](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Deep Security da Trend Micro](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Configure qualquer outra solução
Se um conector não existir para sua solução de segurança específica, use as seguintes instruções genéricas para encaminhar logs para o agente CEF.

1. Acesse o artigo de configuração específico para obter etapas sobre como configurar sua solução para enviar mensagens CEF. Se a sua solução não estiver listada, no aparelho você precisa definir esses valores para que o aparelho envie os registros necessários no formato necessário para o agente Azure Sentinel Syslog, com base no agente Log Analytics. Você pode modificar esses parâmetros em seu aparelho, desde que você também modifique-os no daemon Syslog no agente Azure Sentinel.
    - Protocolo = TCP
    - Porto = 514
    - Formato = CEF
    - Endereço IP - certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que você dedicou para este fim.

   > [!NOTE]
   > Esta solução suporta Syslog RFC 3164 ou RFC 5424.


1. Para usar o esquema relevante no Log Analytics para `CommonSecurityLog`os eventos da CEF, procure .

1. Continue até o PASSO 3: [valide a conectividade](connect-cef-verify.md).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

