---
title: Como se conectar a uma VM dos Azure Lab Services no Mac | Microsoft Docs
description: Saiba como se conectar a partir de um Mac a uma máquina virtual nos Azure Lab Services.
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
ms.openlocfilehash: 9087e4fb7989f89c19a70afd9ee8f061f9061166
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704361"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Conecte-se a uma VM usando o Protocolo de Área de Trabalho Remota em um Mac
Esta seção mostra como um aluno pode se conectar a uma VM de laboratório de sala de aula a partir de um Mac usando a RDP.

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
Para saber como se conectar a VMs do Linux usando RDP, consulte [Usar área de trabalho remota para máquinas virtuais do Linux](how-to-use-remote-desktop-linux-student.md)


