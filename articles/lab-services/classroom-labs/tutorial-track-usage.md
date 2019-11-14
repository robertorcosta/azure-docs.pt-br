---
title: Acompanhar o uso de um laboratório no Azure Lab Services | Microsoft Docs
description: Neste tutorial, você, como um criador/proprietário de laboratório, acompanha o uso do laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 315ebfa1460f9d9bc041925cec2451f63ac5be16
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580283"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Tutorial: acompanhar o uso de um laboratório no Azure Lab Services
Este tutorial mostra como um criador/proprietário de laboratório pode acompanhar o uso de um laboratório.

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Exibir usuários registrados em seu laboratório
> * Exibir o uso de VMs no laboratório
> * Gerenciar VMs de aluno 


## <a name="view-users-registered-with-the-lab"></a>Exibir usuários registrados no laboratório

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). 
2. Selecione **Iniciar sessão** e insira suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft.
3. Na página **Meus Laboratórios**, selecione o laboratório do qual você deseja acompanhar o uso. 
4. Selecione **Usuários** no menu à esquerda ou no bloco **Usuários**. Você verá os alunos que foram registrados no laboratório. Selecione **Link de registro**, copie o link e envie-o a um novo aluno que ainda não tenha sido registrado no laboratório. 

    ![Usuários registrados](../media/tutorial-track-usage/registered-users.png)

    Para obter mais detalhes sobre como adicionar e gerenciar usuários para o laboratório, confira [Adicionar e gerenciar os usuários do laboratório](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms-in-the-lab"></a>Exibir o uso de VMs no laboratório 

1. Selecione **Máquinas Virtuais** no menu à esquerda. 
2. Confirme se você vê o status das máquinas virtuais e o número de horas em que as VMs estão em execução. O tempo que um proprietário de laboratório gasta em uma VM de aluno não é contado em relação ao tempo de uso mostrado na última coluna. 

    ![Uso de VM](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gerenciar VMs de aluno 
Nessa página, você pode iniciar, interromper ou redefinir as VMs dos alunos usando os controles na coluna **Estado** ou na barra de ferramentas.

![Controles da VM](../media/tutorial-track-usage/vm-controls.png)

Para obter mais detalhes sobre como gerenciar um pool de máquinas virtuais para o laboratório, confira [Configurar e gerenciar um pool de máquinas virtuais](how-to-set-virtual-machine-passwords.md).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os laboratórios da sala de aula, confira os artigos nos [Guias de instruções](how-to-manage-lab-accounts.md).
