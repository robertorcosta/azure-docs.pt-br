---
title: Valide a conectividade com o Azure Sentinel| Microsoft Docs
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588426"
---
# <a name="step-3-validate-connectivity"></a>PASSO 3: Validar a conectividade



Depois de implantar o agente e configurar sua solução de segurança para encaminhar mensagens CEF, use este artigo para entender como verificar a conectividade entre o Azure Sentinel e sua solução de segurança. 

## <a name="how-to-validate-connectivity"></a>Como validar a conectividade

1. Abra o Log Analytics para garantir que os logs sejam recebidos usando o esquema CommonSecurityLog.<br> Pode levar mais de 20 minutos até que seus registros comecem a aparecer no Log Analytics. 

1. Antes de executar o script, recomendamos que você envie mensagens de sua solução de segurança para ter certeza de que elas estão sendo encaminhadas para a máquina proxy Syslog que você configurou. 
1. Você deve ter permissões elevadas (sudo) em sua máquina. Certifique-se de que você tem Python na máquina usando o seguinte comando:`python –version`
1. Execute o seguinte script para verificar a conectividade entre o agente, o Azure Sentinel e sua solução de segurança. Ele verifica se o encaminhamento do daemon está configurado corretamente, ouve as portas corretas e que nada está bloqueando a comunicação entre o daemon e o agente log analytics. O script também envia mensagens falsas 'TestCommonEventFormat' para verificar a conectividade de ponta a ponta. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.

