---
title: Implantar o agente para conectar dados do CEF ao Azure Sentinel Preview| Microsoft Docs
description: Saiba como implantar o agente para conectar dados do CEF ao Azure Sentinel.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588460"
---
# <a name="step-1-deploy-the-agent"></a>Passo 1: Implantar o agente


Nesta etapa, você precisa selecionar a máquina Linux que atuará como um proxy entre o Azure Sentinel e sua solução de segurança. Você terá que executar um script na máquina proxy que:
- Instala o agente Log Analytics e configura-o conforme necessário para ouvir mensagens do Syslog.
- Configura o daemon Syslog para ouvir mensagens syslog usando a porta TCP 514 e, em seguida, encaminha apenas as mensagens CEF para o agente Log Analytics usando a porta TCP 25226.
- Define o agente Syslog para coletar os dados e enviá-los com segurança para o Azure Sentinel, onde é analisado e enriquecido.
 
## <a name="deploy-the-agent"></a>Implantar o agente
 
1. No portal Do Azure Sentinel, clique em **Conectores de dados** e selecione **Common Event Format (CEF)** e, em seguida, **abra a página do conector**. 

1. Em **Instalar e configurar o agente Syslog,** selecione o tipo de máquina, azure, outra nuvem ou no local. 
   > [!NOTE]
   > Como o script na etapa seguinte instala o agente Log Analytics e conecta a máquina ao seu espaço de trabalho do Azure Sentinel, certifique-se de que esta máquina não esteja conectada a nenhum outro espaço de trabalho.
1. Você deve ter permissões elevadas (sudo) em sua máquina. Certifique-se de que você tem Python na máquina usando o seguinte comando:`python –version`

1. Execute o seguinte script em sua máquina proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Enquanto o script estiver em execução, verifique se você não recebe nenhum erro ou mensagens de aviso.

Continue até [o PASSO 2: Configure sua solução de segurança para encaminhar mensagens CEF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

