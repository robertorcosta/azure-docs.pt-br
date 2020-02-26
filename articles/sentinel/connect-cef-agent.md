---
title: Implantar o agente para conectar dados de CEF à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como implantar o agente para conectar dados de CEF ao Azure Sentinel.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588460"
---
# <a name="step-1-deploy-the-agent"></a>Etapa 1: implantar o agente


Nesta etapa, você precisa selecionar o computador Linux que atuará como um proxy entre o Azure Sentinel e sua solução de segurança. Você precisará executar um script no computador proxy que:
- Instala o agente de Log Analytics e o configura conforme necessário para escutar mensagens de syslog.
- Configura o daemon do syslog para escutar mensagens de syslog usando a porta TCP 514 e, em seguida, encaminha apenas as mensagens CEF para o agente de Log Analytics usando a porta TCP 25226.
- Define o agente de syslog para coletar os dados e enviá-los com segurança para o Azure Sentinel, onde ele é analisado e aprimorado.
 
## <a name="deploy-the-agent"></a>Implantar o agente
 
1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione o **formato de evento comum (CEF)** e, em seguida, **abra a página conector**. 

1. Em **instalar e configurar o agente de syslog**, selecione o tipo de computador, o Azure, outra nuvem ou local. 
   > [!NOTE]
   > Como o script na próxima etapa instala o agente de Log Analytics e conecta o computador ao seu espaço de trabalho do Azure Sentinel, verifique se este computador não está conectado a nenhum outro espaço de trabalho.
1. Você deve ter permissões elevadas (sudo) em seu computador. Verifique se você tem o Python em seu computador usando o seguinte comando: `python –version`

1. Execute o script a seguir no computador proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Enquanto o script estiver em execução, verifique se você não recebe mensagens de erro ou de aviso.

Continue na [etapa 2: configurar sua solução de segurança para encaminhar mensagens CEF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar os dispositivos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

