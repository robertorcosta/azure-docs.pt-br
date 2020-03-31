---
title: Definir senhas para VMs no Azure Lab Services | Microsoft Docs
description: Aprenda a definir e redefinir senhas para máquinas virtuais (VMs) em laboratórios de sala de aula do Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933797"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurar e gerenciar o pool de máquinas virtuais 
Este artigo mostra como fazer as seguintes tarefas:

- Aumente o número de máquinas virtuais (VMs) no laboratório
- Inicie todas as VMs ou VMs selecionadas 
- Redefinir VMs

## <a name="update-the-lab-capacity"></a>Atualize a capacidade do laboratório
Para aumentar ou diminuir a capacidade de laboratório (número de máquinas virtuais em um laboratório), faça as seguintes etapas:

1. Na página **do pool de máquinas virtuais,** selecione **capacidade do Laboratório: &lt;máquinas numéricas&gt; **.
2. Digite o novo **número de VMs** que você deseja no laboratório. Esse número deve ser maior ou igual ao número de usuários cadastrados no laboratório. 
3. Em seguida, **selecione Salvar**. 

    ![Inicie todos os botões](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Se você aumentou a capacidade, poderá ver as VM ou VMs sendo criadas. Se você não ver a nova VM na lista, atualize a página. 

    ![VM sendo criado](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Iniciar VMs

### <a name="start-ot-stop-all-vms"></a>Iniciar ot parar todas as VMs
1. Mude para a página **do pool de máquinas virtuais.** 
2. Selecione **Iniciar tudo** na barra de ferramentas. 

    ![Inicie todos os botões](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Depois que todas as VMs forem iniciadas, você pode parar todas as VMs selecionando o botão **Parar todos** na barra de ferramentas. 

    ![Pare todos os botões](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Iniciar VMs selecionadas
Existem duas maneiras de iniciar VMs selecionadas (uma ou mais). A primeira maneira é selecionar as VMs ou VMs na lista e, em seguida, selecionar **Iniciar** na barra de ferramentas. 

A segunda maneira é selecionar uma ou mais VMs na lista e alternar o botão na coluna **Estado.** 

![Iniciar VMs selecionadas](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Da mesma forma, você pode parar uma ou mais VMs alternando o botão na coluna **Estado** ou selecionando **Parar** na barra de ferramentas. 

> [!NOTE]
> Quando um educador liga uma VM estudantil, a cota para o aluno não é afetada. A cota para um usuário especifica o número de horas de laboratório disponíveis para o usuário fora do horário de aula programado. Para obter mais informações sobre cotas, consulte [Definir cotas para usuários](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Redefinir VMs
Para redefinir uma ou mais VMs, selecione-as na lista e selecione **Redefinir** na barra de ferramentas. 

![Redefinir VMs selecionados](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Na caixa de diálogo **Redefinir máquina virtual,selecione** **Redefinir**. 

![Redefinir caixa de diálogo VM](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Definir senha para VMs
Um proprietário de laboratório (professor) pode definir/redefinir a senha para VMs no momento da criação do laboratório (assistente de criação de laboratório) ou depois de criar o laboratório na página **Template.** 

### <a name="set-password-at-the-time-of-lab-creation"></a>Definir senha no momento da criação do laboratório
Um dono de laboratório (professor) pode definir uma senha para VMs no laboratório na página **de credenciais** de máquina virtual do assistente de criação de laboratório.

![Janela Novo laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Ao ativar/desativar a **mesma senha para todas as máquinas virtuais** nesta página, um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, esta configuração está habilitada para todas as imagens do sistema operacional Windows e Linux, exceto o Ubuntu. Quando essa configuração estiver desativada, os alunos serão solicitados a definir uma senha quando tentarem se conectar à VM pela primeira vez. 

### <a name="reset-password-later"></a>Redefinir senha mais tarde

1. Na página **Modelo** do laboratório, selecione **Redefinir senha** na barra de ferramentas. 
1. Na caixa de diálogo **Redefinir senha,** digite uma senha e selecione **Redefinir senha**.
    
    ![Definir caixa de diálogo de senha](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Conecte-se às VMs estudantis
O criador do laboratório (instrutor/professor) pode se conectar a uma VM estudantil se as seguintes condições forem atendidas: 

- A **opção Usar a mesma senha para todas as máquinas virtuais** foi selecionada ao criar o laboratório
- A VM está funcionando 

 Para conectar-se à VM do aluno, passe o mouse na VM da lista e selecione o botão do computador.  

![Conecte-se ao botão VM do aluno](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Quando o professor inicia a VM e se conecta a ela, a cota de alunos não é afetada. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Lista de exportação de máquinas virtuais para um arquivo CSV

1. Mude para a guia **Pool de máquinas virtuais.**
2. Selecione **...** (elipse) na barra de ferramentas e, em seguida, selecione **Exportar CSV**. 

    ![Lista de exportação de máquinas virtuais](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre outras opções de uso do aluno que você (como proprietário de laboratório) pode configurar, consulte o seguinte artigo: [Configure](how-to-configure-student-usage.md)o uso do aluno .

Para saber como os alunos podem redefinir senhas para suas VMs, consulte [Definir ou redefinir senha para máquinas virtuais em laboratórios de sala de aula (alunos)](how-to-set-virtual-machine-passwords-student.md).