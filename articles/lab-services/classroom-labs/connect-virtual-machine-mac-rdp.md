---
title: Como se conectar a uma VM de Serviços de Laboratório azure da Mac | Microsoft Docs
description: Este artigo
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503082"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Conectar-se a uma VM usando RDP em um Mac
Esta seção mostra como um aluno pode se conectar a um VM de laboratório de sala de aula de um Mac usando RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instalar a Área de Trabalho Remota da Microsoft em um Mac
1. Abra a App Store em seu Mac e procure pela **Área de Trabalho Remota da Microsoft**.

    ![Área de Trabalho Remota da Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Instale a versão mais recente da Área de Trabalho Remota da Microsoft. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Acessar a VM de seu Mac usando RDP
1. Abra o arquivo **RDP** que é baixado em seu computador com a **Área de Trabalho Remota da Microsoft** instalada. Ele deve começar a se conectar à VM. 

    ![Conectar-se a uma VM](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Selecione **Continuar** se você receber o aviso a seguir. 

    ![Aviso de certificado](../media/how-to-use-classroom-lab/certificate-error.png)
1. Você deverá ver a VM. 

    > [!NOTE]
    > O exemplo a seguir é referente a uma VM CentOS do Linux. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Próximas etapas
Para saber como se conectar a VMs Linux usando RDP, consulte [Usar desktop remoto para máquinas virtuais Linux](how-to-use-remote-desktop-linux-student.md)


