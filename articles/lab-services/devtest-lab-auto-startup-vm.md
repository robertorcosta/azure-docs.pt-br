---
title: Configure configurações de partida automática para um VM no Azure DevTest Labs | Microsoft Docs
description: Saiba como configurar configurações de partida automática para VMs em um laboratório. Esta configuração permite que as VMs no laboratório sejam automaticamente iniciadas em um cronograma.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807908"
---
# <a name="auto-startup-lab-virtual-machines"></a>Máquinas virtuais de laboratório de startup automática  
O Azure DevTest Labs permite que você configure máquinas virtuais em seu laboratório para serem automaticamente iniciadas e desligadas com base em um cronograma. Para obter informações sobre a configuração de configurações de desligamento automático, consulte [Gerenciar políticas de desligamento automático para um laboratório no Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

Ao contrário do desligamento automático, onde todas as VMs são incluídas quando a diretiva é ligada, a diretiva de início automático exige que um usuário de laboratório selecione explicitamente uma VM e opte por esse cronograma. Dessa forma, você não vai facilmente correr para a situação onde VMs indesejados são acidentalmente iniciados automaticamente e causar gastos inesperados.

Este artigo mostra como configurar a política de inicializar automaticamente para um laboratório.

## <a name="configure-autostart-settings-for-a-lab"></a>Configure configurações de inicialação automática para um laboratório 
1. Navegue até a página inicial do seu laboratório. 
2. Selecione **Configuração e políticas** no menu esquerdo. 

    ![Menu de configuração e políticas](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Na página **Configuração e políticas,** faça as seguintes etapas:
    
    1. Selecione **Ativado** para **permitir que as máquinas virtuais sejam agendadas para iniciar automaticamente** para habilitar o recurso de partida automática para este laboratório. 
    2. Selecione um horário de início (por exemplo: 8:00:00 AM) para o campo **de início de programação.** 
    3. Selecione um **fuso horário** a ser usado. 
    4. Selecione **os dias da semana** em que as VMs precisam ser iniciadas automaticamente. 
    5. Em seguida, **selecione Salvar** na barra de ferramentas para salvar as configurações. 

        ![Configurações de inicialção automática](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Esta política não aplica automaticamente o desligamento automático de nenhuma máquina virtual no laboratório. Para **optar por** máquinas virtuais individuais, acesse a página da máquina virtual e habilite a partida **automática** para essa VM.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Habilite a partida automática para um VM no laboratório
O procedimento a seguir fornece etapas para optar em uma VM na política de início automático do laboratório. 

1. Na página inicial do seu laboratório, selecione a **VM** na lista **Minhas máquinas virtuais.** 

    ![Menu de configuração e políticas](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Na página **da máquina Virtual,** selecione **Autostart** no menu esquerdo ou na lista **Agendados.** 

    ![Selecione o menu iniciar automaticamente](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Na página **Autostart,** selecione **'Ativar'** **Para permitir que esta máquina virtual seja agendada para a** opção de partida automática.

    ![Habilite a partida automática para a VM](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Em seguida, **selecione Salvar** na barra de ferramentas para salvar a configuração. 


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a política de fechamento automático de configuração de um laboratório, consulte [Gerenciar políticas de desligamento automático para um laboratório no Azure DevTest Labs](devtest-lab-auto-shutdown.md)
