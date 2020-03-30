---
title: Conecte dados de firewall do Windows ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados de firewall do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588052"
---
# <a name="connect-windows-firewall"></a>Conectar firewall do Windows



O conector de firewall do Windows permite conectar facilmente os registros de firewalls do Windows, se eles estiverem conectados ao seu espaço de trabalho do Azure Sentinel. Essa conexão permite que você visualize dashboards, crie alertas personalizados e melhore a investigação. Isso lhe dá mais informações sobre a rede da sua organização e melhora os recursos de operação de segurança. A solução coleta eventos de firewall do Windows das máquinas Windows nas quais um agente do Log Analytics está instalado. 


> [!NOTE]
> - Os dados serão armazenados na localização geográfica do espaço de trabalho em que você está executando o Azure Sentinel.
> - Se o Azure Sentinel e o Azure Security Center forem coletados no mesmo espaço de trabalho, não há necessidade de ativar a solução do Firewall do Windows através deste conector. Se você habilitá-lo de qualquer maneira, ele não causará dados duplicados. 

## <a name="enable-the-connector"></a>Habilite o conector 

1. No portal Do Azure Sentinel, selecione **conectores de dados** e clique no firewall do **Windows.** 
1.  Se suas máquinas Windows estiverem no Azure:
    1. Clique **em Instalar agente na máquina virtual do Azure Windows**.
    1. Na lista **de máquinas virtuais,** selecione a máquina Windows que deseja transmitir no Azure Sentinel. Certifique-se de que este é um VM do Windows.
    1. Na janela que abre para aquela VM, clique **em Conectar**.  
    1. Clique em **Ativar** na janela do **conector de firewall** do Windows. 

2. Se a sua máquina Windows não for uma VM Azure:
    1. Clique **em Instalar agente em máquinas não-Azure**.
    1. Na janela **agente direto,** selecione **Baixar o agente windows (64 bits)** ou **baixar o agente do Windows (32 bits)**.
    1. Instale o agente na sua máquina Windows. Copie o **ID do espaço de trabalho,** **a tecla principal**e **a tecla secundária** e use-as quando solicitada durante a instalação.

4. Selecione quais tipos de dados você deseja transmitir.
5. Clique **em Instalar solução**.
6. Para usar o esquema relevante no Log Analytics para o firewall do Windows, procure **securityevent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus registros comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o firewall do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

