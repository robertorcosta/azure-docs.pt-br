---
title: Habilite o desktop remoto para Linux no Azure Lab Services | Microsoft Docs
description: Aprenda a habilitar desktop remoto para máquinas virtuais Linux em um laboratório no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270856"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Habilite o desktop remoto para máquinas virtuais Linux em um laboratório no Azure Lab Services
Este artigo mostra como fazer as seguintes tarefas:

- Habilite o desktop remoto para Linux VM
- Como o professor pode se conectar ao modelo VM via RDP (Remote Desktop Connection, conexão de desktop remota).

## <a name="enable-remote-desktop-for-linux-vm"></a>Habilite o desktop remoto para Linux VM
Durante a criação do laboratório, os professores podem habilitar **a conexão remota de desktop** para imagens **Linux.** A opção **Ativar conexão remota da área de trabalho** é mostrada quando uma imagem Linux é selecionada para o modelo. Quando essa opção estiver ativada, os professores podem se conectar ao modelo VM e VMs de alunos via RDP (Remote Desktop). 

![Habilite a conexão remota de desktop para uma imagem Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Na caixa de mensagem **Ativar conexão remota da área de trabalho,** selecione **Continuar com desktop remoto**. 

![Habilite a conexão remota de desktop para uma imagem Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Ativar **a conexão remota da área de trabalho** só abre a porta **RDP** em máquinas Linux. Se o RDP já estiver instalado e configurado na imagem da máquina virtual (por exemplo: imagem da Máquina Virtual de Ciência de Dados do Ubuntu), você/alunos podem se conectar às VMs via RDP sem seguir quaisquer etapas adicionais.
> 
> Se a imagem vm não tiver RDP instalado e configurado, você precisa se conectar à máquina Linux usando SSH pela primeira vez e instalar pacotes RDP e GUI para que você/alunos possam se conectar à máquina Linux usando RDP mais tarde. Para obter mais informações, consulte [Instalar e configurar o Remote Desktop para se conectar a uma VM Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md). Em seguida, você publica a imagem para que os alunos possam RDP no aluno Linux VMs. 

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis
Atualmente, a conexão de desktop remota é suportada para os seguintes sistemas operacionais:

- openSUSE Leap 42.3
- 7.5 baseado no CentOS
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Conectar à VM modelo 
Os professores devem se conectar ao modelo VM usando SSH primeiro e instalar pacotes RDP e GUI nele. Em seguida, os professores podem usar RDP para se conectar ao modelo VM: 

1. Se você ver **Personalizar modelo** na barra de ferramentas, selecione-o. Em seguida, **selecione Continuar** na caixa de diálogo **Personalizar modelo.** Esta ação inicia o modelo VM.  

    ![Personalizar o modelo](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Depois que o modelo VM for iniciado, você pode selecionar **conectar o modelo** e, em seguida, conectar via **SSH** na barra de ferramentas. 

    ![Conecte-se ao modelo via RDP depois que o laboratório for criado](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Veja a seguir **Conecte-se à caixa** de diálogo da máquina virtual. Selecione o botão **Copiar** ao lado da caixa de texto para copiá-lo na área de transferência. Salve a seqüência de conexão SSH. Use essa cadeia de conexão de um terminal SSH (como [Putty](https://www.putty.org/)) para conectar a máquina virtual.
 
    ![String de conexão SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Instale pacotes RDP e GUI para que você/alunos possam se conectar à máquina Linux usando RDP mais tarde. Para obter mais informações, consulte [Instalar e configurar o Remote Desktop para se conectar a uma VM Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md). Em seguida, você publica a imagem para que os alunos possam RDP no aluno Linux VMs.
5. Depois que esses pacotes forem instalados, você pode usar o **modelo Conectar à barra** de ferramentas e, em seguida, selecionar Conectar via **RDP** para se conectar ao modelo VM via RDP. Salve o arquivo RDP e use-o para se conectar ao vm do modelo via RDP. 

## <a name="next-steps"></a>Próximas etapas
Depois que um instrutor habilitou o recurso de conexão remota da área de trabalho, os alunos podem se conectar às suas VMs via RDP/SSH. Para obter mais informações, consulte [Use desktop remoto para VMs Linux em um laboratório de sala de aula](how-to-use-remote-desktop-linux-student.md). 