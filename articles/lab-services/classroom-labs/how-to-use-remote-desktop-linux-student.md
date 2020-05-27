---
title: Usar a área de trabalho remota para Linux no Azure Lab Services | Microsoft Docs
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 8ccad0698ea6560dd183cacc71f5f3a644e8220c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588098"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Usar a área de trabalho remota para máquinas virtuais do Linux em um laboratório de sala de aula de Azure Lab Services
Este artigo mostra como os alunos podem se conectar a uma VM (máquina virtual) do Linux em um laboratório usando RDP/SSH. 

Um instrutor precisa habilitar o recurso de conexão de área de trabalho remota antes que os alunos possam se conectar à VM do laboratório de sala de aula. Para obter instruções sobre como um instrutor pode habilitar o recurso de conexão de área de trabalho remota, confira [Habilitar a área de trabalho remota para máquinas virtuais do Linux](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Habilitar a **conexão de área de trabalho remota** abre apenas a porta **RDP** em computadores Linux. Um instrutor pode conectar-se ao computador Linux usando o SSH pela primeira vez e instalar os pacotes GUI e RDP de modo que você possa se conectar ao computador Linux posteriormente usando o RDP. 

## <a name="connect-to-the-student-vm"></a>Conectar-se à VM do aluno
Os alunos podem conectar-se por RDP a suas VMs do Linux após o proprietário do laboratório (educador) **publicar** a VM do modelo com pacotes RDP e GUI instalados no computador. Siga estas etapas: 

1. Quando um aluno entra no portal de Laboratórios diretamente (`https://labs.azure.com`) ou usando um link de registro (`https://labs.azure.com/register/<registrationCode>`), um bloco é exibido para cada laboratório ao qual o aluno tem acesso. 
2. No bloco, alterne o estado do botão para iniciar a VM se ela estiver no estado parado. 
3. Selecione **Conectar**. Você verá duas opções para se conectar à VM: **SSH** e **Área de Trabalho Remota**.

    ![VM do aluno – opções de conexão](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Conectar-se usando SSH ou RDP
Se você selecionar a opção **SSH**, verá a seguinte caixa de diálogo **Conectar-se à sua máquina virtual**:  

![Cadeia de conexão SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selecione o botão **Copiar** ao lado da caixa de texto para copiá-lo para a área de transferência. Salve a cadeia de conexão SSH. Use essa cadeia de conexão de um terminal SSH (como [Putty](https://www.putty.org/)) para conectar a máquina virtual.

Se você selecionar a opção **RDP**, um arquivo RDP será baixado em seu computador. Salve-o e abra-o para se conectar ao computador. 

## <a name="next-steps"></a>Próximas etapas
Para saber como habilitar o recurso de conexão de área de trabalho remota para VMs do Linux em um laboratório de sala de aula, confira [Habilitar a área de trabalho remota para máquinas virtuais do Linux](how-to-enable-remote-desktop-linux.md). 

