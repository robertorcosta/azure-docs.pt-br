---
title: Definir senhas para VMs no Azure Lab Services | Microsoft Docs
description: Saiba como definir e redefinir senhas para VMs (máquinas virtuais) em laboratórios de sala de aula do Azure Lab Services.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78933797"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurar e gerenciar o pool de máquinas virtuais 
Este artigo mostra como realizar as seguintes tarefas:

- Aumentar o número de máquinas virtuais (VMs) no laboratório
- Iniciar todas as VMs ou VMs selecionadas 
- Redefinir VMs

## <a name="update-the-lab-capacity"></a>Atualizar a capacidade do laboratório
Para aumentar ou diminuir a capacidade do laboratório (número de máquinas virtuais em um laboratório), execute as seguintes etapas:

1. Na página **pool de máquinas virtuais** , selecione **capacidade do laboratório &lt;:&gt; número de computadores**.
2. Insira o novo **número de VMs** que você deseja no laboratório. Esse número deve ser maior ou igual ao número de usuários registrados no laboratório. 
3. Em seguida, selecione **salvar**. 

    ![Botão iniciar tudo](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Se você aumentou a capacidade, poderá ver a VM ou as VMs que estão sendo criadas. Se você não vir a nova VM na lista, atualize a página. 

    ![VM sendo criada](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Iniciar VMs

### <a name="start-ot-stop-all-vms"></a>Começar a fazer parar todas as VMs
1. Alterne para a página do **pool de máquinas virtuais** . 
2. Selecione **Iniciar tudo** na barra de ferramentas. 

    ![Botão iniciar tudo](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Depois que todas as VMs forem iniciadas, você poderá interromper todas as VMs selecionando o botão **parar tudo** na barra de ferramentas. 

    ![Botão parar tudo](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Iniciar VMs selecionadas
Há duas maneiras de iniciar as VMs selecionadas (uma ou mais). A primeira maneira é selecionar a VM ou as VMs na lista e, em seguida, selecionar **Iniciar** na barra de ferramentas. 

A segunda maneira é selecionar uma ou mais VMs na lista e alternar o botão na coluna **estado** . 

![Iniciar VMs selecionadas](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Da mesma forma, você pode interromper uma ou mais VMs alternando o botão na coluna **estado** ou selecionando **parar** na barra de ferramentas. 

> [!NOTE]
> Quando um professor acende uma VM de aluno, a cota para o aluno não é afetada. A cota de um usuário especifica o número de horas de laboratório disponíveis para o usuário fora da hora da classe agendada. Para obter mais informações sobre cotas, consulte [definir cotas para usuários](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Redefinir VMs
Para redefinir uma ou mais VMs, selecione-as na lista e, em seguida, selecione **Redefinir** na barra de ferramentas. 

![Redefinir as VMs selecionadas](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Na caixa de diálogo **Redefinir máquina (s) virtual (is)** , selecione **Redefinir**. 

![Caixa de diálogo redefinir VM](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Definir senha para VMs
Um professor (proprietário do laboratório) pode definir/redefinir a senha para VMs no momento da criação do laboratório (Assistente de criação de laboratório) ou depois de criar o laboratório na página de **modelo** . 

### <a name="set-password-at-the-time-of-lab-creation"></a>Definir a senha no momento da criação do laboratório
Um professor (proprietário do laboratório) pode definir uma senha para VMs no laboratório na página de **credenciais da máquina virtual** do assistente de criação de laboratório.

![Janela Novo laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Ao habilitar/desabilitar a opção **usar a mesma senha para todas as máquinas virtuais** nesta página, um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, essa configuração é habilitada para todas as imagens do sistema operacional Windows e Linux, exceto o Ubuntu. Quando essa configuração estiver desabilitada, os alunos serão solicitados a definir uma senha quando tentarem se conectar à VM pela primeira vez. 

### <a name="reset-password-later"></a>Redefinir senha mais tarde

1. Na página **modelo** do laboratório, selecione **Redefinir senha** na barra de ferramentas. 
1. Na caixa de diálogo **Redefinir senha** , insira uma senha e selecione **Redefinir senha**.
    
    ![Caixa de diálogo Definir senha](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Conectar-se às VMs dos alunos
O criador de laboratório (instrutor/professor) pode se conectar a uma VM de aluno se as seguintes condições forem atendidas: 

- A opção **usar a mesma senha para todas as máquinas virtuais** foi selecionada ao criar o laboratório
- A VM está em execução 

 Para se conectar à VM do aluno, passe o mouse sobre a VM na lista e selecione o botão computador.  

![Botão conectar ao aluno da VM](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Quando o professor inicia a VM e se conecta a ela, a cota do aluno não é afetada. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Exportar a lista de máquinas virtuais para um arquivo CSV

1. Alterne para a guia **pool de máquinas virtuais** .
2. Selecione **...** (reticências) na barra de ferramentas e, em seguida, selecione **Exportar CSV**. 

    ![Exportar lista de máquinas virtuais](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as outras opções de uso do aluno que você (como proprietário do laboratório) podem configurar, consulte o seguinte artigo: [Configurar o uso do aluno](how-to-configure-student-usage.md).

Para saber mais sobre como os alunos podem redefinir senhas para suas VMs, consulte [definir ou Redefinir senha para máquinas virtuais em laboratórios de sala de aula (estudantes)](how-to-set-virtual-machine-passwords-student.md).