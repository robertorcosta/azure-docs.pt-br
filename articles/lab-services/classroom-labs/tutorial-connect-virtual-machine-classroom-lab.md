---
title: Acessar um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Neste tutorial, você deve acessar máquinas virtuais em um laboratório de sala de aula configurado por um professor.
services: devtest-lab, lab-services, virtual-machines
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 27d79e28a986e929fb71dd77fc50b3c2cd32618f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77134032"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Tutorial: Acessar um laboratório de sala de aula no Azure Lab Services
Neste tutorial, você, como aluno, conecta-se a uma máquina virtual (VM) em um laboratório de sala de aula. 

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Registrar-se no laboratório
> * Iniciar a VM
> * Conectar-se à VM

## <a name="register-to-the-lab"></a>Registrar-se no laboratório

1. Navegue até a **URL de registro** que você recebeu do professor/educador. Não é necessário usar a URL de registro após concluir o registro. Em vez disso, use a URL: [https://labs.azure.com](https://labs.azure.com). Ainda não há suporte para o Internet Explorer 11. 
1. Entre no serviço usando sua conta da escola para concluir o registro. 

    > [!NOTE]
    > Uma conta Microsoft é necessária para usar o Azure Lab Services. Se estiver tentando usar sua conta não Microsoft, como as contas do Yahoo ou do Google, para entrar no portal, siga as instruções para criar uma conta Microsoft que será vinculada à sua conta não Microsoft. Em seguida, siga as etapas para concluir o processo de registro. 
1. Depois de registrado, confirme que consegue ver a máquina virtual do laboratório ao qual você tem acesso. 
1. Aguarde até a máquina virtual ficar pronta. No bloco da VM, observe os seguintes campos:
    1. Na parte superior do bloco, você verá o **nome do laboratório**.
    1. À direita, você verá o ícone que representa o **SO (sistema operacional)** da VM. Neste exemplo, é o sistema operacional Windows. 
    1. A barra de progresso no bloco mostra o número de horas usadas em relação ao número de [horas de cota](how-to-configure-student-usage.md#set-quotas-for-users) atribuídas a você. Esse tempo é o tempo adicional alocado para você, além do horário agendado para o laboratório. 
    1. Você verá os ícones/os botões na parte inferior do bloco para iniciar/interromper a VM e se conectar a ela. 
    1. À direita dos botões, você verá o status da VM. Confirme se o status da VM mostrado está **Interrompida**. 

        ![VM no estado Interrompido](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Iniciar a VM
1. **Inicie** a VM selecionando o primeiro botão, conforme mostrado na imagem a seguir. Esse processo demora um pouco.  

    ![Iniciar a VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Confirme se o status da VM está definido como **Em execução**. 

    ![VM no estado de execução](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Observe que o ícone do primeiro botão foi alterado para representar uma operação de **interrupção**. Selecione esse botão para interromper a VM. 

## <a name="connect-to-the-vm"></a>Conectar-se à VM

1. Selecione o segundo botão, conforme mostrado na imagem a seguir, para se **conectar** à VM do laboratório. 

    ![Conectar-se a uma VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Siga uma das etapas a seguir: 
    1. Para máquinas virtuais do **Windows**, salve o arquivo **RDP** no disco rígido. Abra o arquivo RDP para conectar a máquina virtual. Use o **nome de usuário** e **senha** obtido do seu professor/educador para entrar no computador. 
    3. Para máquinas virtuais do **Linux**, você pode usar **SSH** ou **RDP** (se habilitado) para se conectar a elas. Para obter mais informações, veja [Habilitar conexão de área de trabalho remota para computadores Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você acessou um laboratório de sala de aula usando o link de registro obtido do seu professor/educador.

Como proprietário de laboratório, convém exibir quem se registrou em seu laboratório e acompanhar o uso das VMs. Vá para o próximo tutorial para aprender a controlar o uso do laboratório:

> [!div class="nextstepaction"]
> [Acompanhar o uso de um laboratório](tutorial-track-usage.md) 
