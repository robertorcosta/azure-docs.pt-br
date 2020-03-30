---
title: Conecte os dados do F5 ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados F5 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588256"
---
# <a name="connect-f5-to-azure-sentinel"></a>Conecte f5 ao Azure Sentinel

Este artigo explica como conectar seu aparelho F5 ao Azure Sentinel. O conector de dados F5 permite conectar facilmente seus registros F5 com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. O uso do F5 no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e melhorará seus recursos de operação de segurança. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Configure seu F5 para enviar mensagens CEF

1. Vá para [F5 Configurando o registro de eventos de segurança](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)do aplicativo e siga as instruções para configurar o registro remoto, usando as seguintes diretrizes:
   - Defina o **tipo de armazenamento remoto** como **CEF**.
   - Defina o **protocolo** como **TCP**.
   - Defina o **endereço IP** no endereço IP do servidor Syslog.
   - Defina o número da **porta** como **514**ou a porta que você definir á sua agente para usar.
   - Você pode definir o **tamanho máximo da corda de consulta** para o tamanho definido em seu agente.

1. Para usar o esquema relevante no Log Analytics para `CommonSecurityLog`os eventos da CEF, procure .

1. Continue até [o PASSO 3: Valide a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar f5 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.

