---
title: Use desktop remoto para Linux no Azure Lab Services | Microsoft Docs
description: Aprenda a usar desktop remoto para máquinas virtuais Linux em um laboratório no Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585097"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Use desktop remoto para máquinas virtuais Linux em um laboratório de sala de aula do Azure Lab Services
Este artigo mostra como os alunos podem se conectar a uma máquina virtual Linux (VM) em um laboratório usando RDP/SSH. 

Um instrutor precisa habilitar o recurso de conexão remota da área de trabalho antes que os alunos possam se conectar à VM do laboratório de sala de aula. Para obter instruções sobre como um instrutor pode habilitar o recurso de conexão remota da área de trabalho, consulte [Habilitar desktop remoto para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Ativar **a conexão remota da área de trabalho** só abre a porta **RDP** em máquinas Linux. Um instrutor pode se conectar à máquina Linux usando SSH pela primeira vez, e instalar pacotes RDP e GUI para que você possa se conectar à máquina Linux usando RDP mais tarde. 

## <a name="connect-to-the-student-vm"></a>Conecte-se ao vm estudantil
Os alunos podem rdP em seus VMs Linux depois que o proprietário do laboratório (professor/professor) **publica** o modelo VM com pacotes RDP e GUI instalados na máquina. Siga estas etapas: 

1. Quando um aluno entra diretamente no`https://labs.azure.com`portal do Labs ou`https://labs.azure.com/register/<registrationCode>`por meio de um link de inscrição ( ), uma telha para cada laboratório a que o aluno tem acesso é exibida. 
2. No azulejo, alterne o botão para ligar a VM se estiver em estado parado. 
3. Selecione **Conectar**. Você vê duas opções para se conectar à VM: **SSH** e **Remote Desktop**.

    ![Estudante VM - opções de conexão](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Conecte-se usando SSH ou RDP
Se você selecionar a opção **SSH,** verá o seguinte **Conectar-se à sua** caixa de diálogo de máquina virtual:  

![String de conexão SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selecione o botão **Copiar** ao lado da caixa de texto para copiá-lo na área de transferência. Salve a seqüência de conexão SSH. Use essa cadeia de conexão de um terminal SSH (como [Putty](https://www.putty.org/)) para conectar a máquina virtual.

Se você selecionar a opção **RDP,** um arquivo RDP será baixado na sua máquina. Guarde-o e abra-o para se conectar à máquina. 

## <a name="next-steps"></a>Próximas etapas
Para saber como ativar o recurso de conexão de desktop remota para VMs Linux em um laboratório de sala de aula, consulte [Habilitar desktop remoto para máquinas virtuais Linux](how-to-enable-remote-desktop-linux.md). 

