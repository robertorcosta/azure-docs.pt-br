---
title: Conecte os dados do Barracuda ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados barracuda ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588477"
---
# <a name="connect-your-barracuda-appliance"></a>Conecte seu aparelho Barracuda 



O conector WAF (Web Application Firewall, firewall de aplicativos da Web) barracuda permite conectar facilmente seus logs barracuda com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. Isso lhe dá mais informações sobre a rede da sua organização e melhora os recursos de operação de segurança. O Azure Sentinel aproveita a integração nativa entre o **Barracuda** e o agente Log Analytics para proporcionar uma integração perfeita. 


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que você está executando o Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configure e conecte barracuda WAF
O Barracuda Web Application Firewall pode integrar e exportar logs diretamente para o Azure Sentinel via agente Log Analytics.
1. Vá para [barracuda waf fluxo de configuração](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/), e siga as instruções para configurar a conexão, usando estes parâmetros:
    - **ID do espaço de trabalho**: copie o valor do seu ID do espaço de trabalho na página do conector Azure Sentinel Barracuda.
    - **Chave principal:** copie o valor da sua chave principal na página do conector Azure Sentinel Barracuda.
1. Para usar o esquema relevante no Log Analytics para os eventos Barracuda, procure **por CommonSecurityLog** e **barracuda_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus registros comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar os aparelhos Barracuda ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.


