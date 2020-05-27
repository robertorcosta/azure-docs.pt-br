---
title: Habilitar a área de trabalho remota para Linux no Azure Lab Services | Microsoft Docs
description: Saiba como usar a área de trabalho remota para máquinas virtuais do Linux em um laboratório no Azure Lab Services.
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
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a40575340fc5b1c202be6b001807085954439f03
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588149"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Habilitar a área de trabalho remota para máquinas virtuais do Linux em um laboratório no Azure Lab Services
Este artigo mostra como fazer as seguintes tarefas:

- Habilitar área de trabalho remota para a VM do Linux
- Como um educador pode se conectar à VM modelo por da Conexão de Área de Trabalho Remota (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Habilitar área de trabalho remota para a VM do Linux
Durante a criação do laboratório, os educadores podem habilitar a **conexão de área de trabalho remota** para imagens do **Linux**. A opção **Habilitar Conexão de Área de Trabalho Remota** é mostrada quando uma imagem do Linux é selecionada para o modelo. Quando essa opção está habilitada, educadores podem se conectar à VM de modelo e VMs de aluno via RDP (Área de Trabalho Remota). 

![Habilitar a conexão de área de trabalho remota para uma imagem do Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Na caixa de mensagem **Habilitar Conexão de Área de Trabalho Remota**, selecione **Continuar com Área de Trabalho Remota**. 

![Habilitar a conexão de área de trabalho remota para uma imagem do Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Habilitar a **conexão de área de trabalho remota** abre apenas a porta **RDP** em computadores Linux. Se o RDP já estiver instalado e configurado na imagem da máquina virtual, você e os alunos poderão se conectar a VMs pelo RDP sem seguir nenhuma etapa adicional.
> 
> Se a imagem da VM não tiver o RDP instalado e configurado, você precisará se conectar ao computador Linux usando o SSH pela primeira vez e instalar os pacotes GUI e RDP para que você e os alunos possam se conectar ao computador Linux usando o RDP posteriormente. Para obter mais informações, consulte [Instalar e configurar a Área de Trabalho Remota para se conectar a uma VM do Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md). Em seguida, publique a imagem para que os alunos possam se conectar ao RDP nas VMs do Linux do aluno. 

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis
Atualmente, a conexão de área de trabalho remota tem suporte para os seguintes sistemas operacionais:

- openSUSE Leap 42.3
- Baseado em CentOS 7.5
- Debian 9 “Stretch”
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Conectar à VM modelo 
Educadores devem se conectar à VM modelo usando SSH primeiro e instalar pacotes RDP e GUI nela. Em seguida, os educadores podem usar o RDP para se conectar à VM modelo: 

1. Se você vir **Personalizar o modelo** na barra de ferramentas, selecione-o. Em seguida, selecione **Continuar** na caixa de diálogo **Personalizar modelo**. Essa ação inicia a VM modelo.  

    ![Personalizar modelo](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Depois que a VM modelo for iniciada, você poderá selecionar **Modelo de conexão** e, em seguida, **Conectar-se via SSH** na barra de ferramentas. 

    ![Conectar-se ao modelo via RDP após a criação do laboratório](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Você verá a seguinte caixa de diálogo: **Conectar-se à sua máquina virtual**. Selecione o botão **Copiar** ao lado da caixa de texto para copiá-lo para a área de transferência. Salve a cadeia de conexão SSH. Use essa cadeia de conexão de um terminal SSH (como [Putty](https://www.putty.org/)) para conectar a máquina virtual.
 
    ![Cadeia de conexão SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Instale pacotes RDP e GUI para que você e os alunos possam se conectar ao computador Linux usando o RDP mais tarde. Para obter mais informações, consulte [Instalar e configurar a Área de Trabalho Remota para se conectar a uma VM do Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md). Em seguida, publique a imagem para que os alunos possam se conectar ao RDP nas VMs do Linux do aluno.
5. Depois que esses pacotes são instalados, você poderá usar a opção **Conectar-se ao modelo** na barra de ferramentas e, em seguida, selecionar **Conectar-se via RDP** para se conectar à VM modelo via RDP. Salve o arquivo RDP e use-o para se conectar à VM modelo via RDP. 

## <a name="next-steps"></a>Próximas etapas
Depois que um instrutor habilitou o recurso de conexão de área de trabalho remota, os alunos podem se conectar às suas VMs por meio de RDP/SSH. Para obter mais informações, consulte [Usar a área de trabalho remota para VMs do Linux em um laboratório de sala de aula](how-to-use-remote-desktop-linux-student.md). 