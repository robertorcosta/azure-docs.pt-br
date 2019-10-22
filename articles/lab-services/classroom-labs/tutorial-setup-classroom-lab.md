---
title: Configurar um laboratório de sala de aula usando o Azure Lab Services | Microsoft Docs
description: Neste tutorial, você configura um laboratório para usar em uma sala de aula.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: fe40eb27b07304aba48be4a47fb22168cb60434c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332264"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, você configura um laboratório de sala de aula com máquinas virtuais que são usadas por alunos na sala de aula.  

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Adicionar usuários ao laboratório
> * Definir uma agenda para o laboratório
> * Enviar um email de convite para os alunos

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, é necessário ser membro de uma destas funções na conta de laboratório: Proprietário, Criador de Laboratório ou Colaborador. A conta que você usou para criar uma conta de laboratório é adicionada automaticamente à função de proprietário.

Um proprietário de laboratório pode adicionar outros usuários à função **Criador de Laboratório**. Por exemplo, um proprietário de laboratório adiciona professores à função Criador de Laboratório. Em seguida, os professores criam laboratórios com VMs para suas aulas. Os alunos usam o link de registro que recebem dos professores para registrarem-se para o laboratório. Depois de registrados, eles podem usar VMs nos laboratórios para fazerem os trabalhos de aula e os deveres de casa. Para obter etapas detalhadas para adicionar usuários à função Criador de Laboratório, confira [Adicionar um usuário à função Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). Observe que ainda não há suporte para o Internet Explorer 11. 
2. Selecione **Iniciar sessão** e insira suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Selecione **Novo laboratório**. 
    
    ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório e selecione **Avançar**.  

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, veja [Habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).
    2. Na página **Credenciais da máquina virtual**, especifique as credenciais padrão para todas as VMs no laboratório. Especifique o **nome** e a **senha** do usuário e, em seguida, selecione **Avançar**.  

        ![Janela Novo laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.
    3. Na página **Políticas do laboratório**, insira o número de horas alocadas para cada usuário (**cota para cada usuário**) fora da hora agendada para o laboratório e, em seguida, selecione **Concluir**. 

        ![Cota para cada usuário](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Você deverá ver a tela a seguir que mostra o status da criação da VM de modelo. A criação do modelo no laboratório leva até 20 minutos. 

    ![Status da criação da VM de modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **Modelo**, execute as seguintes etapas: Essas etapas são **opcionais** para o tutorial.

    2. Conecte-se à VM modelo selecionando **Conectar**. Se for uma VM de modelo do Linux, você escolherá se deseja se conectar usando SSH ou RDP (se RDP estiver habilitado).
    1. Selecione **Redefinição de senha** para redefinir a senha da VM. 
    1. Instale e configure software em sua VM modelo. 
    1. **Pare** a VM.  
    1. Insira uma **descrição** do modelo
10. Na página **Modelo**, selecione **Publicar** na barra de ferramentas. 

    ![Botão Publicar modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, você não pode cancelar a publicação. 
8. Na página **Publicar modelo**, insira o número de máquinas virtuais que deseja criar no laboratório e, em seguida, selecione **Publicar**. 

    ![Publicar modelo – número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Você verá o **status de publicação** do modelo na página. Esse processo pode levar até uma hora. 

    ![Publicar modelo – andamento](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Alterne para a página **Pool de máquinas virtuais** selecionando Máquinas virtuais no menu esquerdo ou selecionando o bloco Máquinas virtuais. Confirme que você vê cinco máquinas virtuais no estado **Não atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Execute as seguintes tarefas nessa página (não execute estas etapas para o tutorial. Essas etapas são apenas para fins informativos): 
    
    1. Para alterar a capacidade do laboratório (número de VMs no laboratório), selecione **Capacidade do laboratório** na barra de ferramentas.
    2. Para iniciar todas as VMs de uma só vez, selecione **Iniciar todas** na barra de ferramentas. 
    3. Para iniciar uma VM específica, selecione a seta para baixo no **Status** e, em seguida, selecione **Iniciar**. Inicie também uma VM selecionando uma VM na primeira coluna e, em seguida, selecionando **Iniciar** na barra de ferramentas.

## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório

1. Selecione **Usuários** no menu à esquerda. Por padrão, a opção **Restringir acesso** está habilitada. Quando essa configuração está habilitada, um usuário não pode se registrar com o laboratório mesmo que o usuário tenha o link de registro, a menos que o usuário esteja na lista de usuários. Somente os usuários na lista podem se registrar no laboratório usando o link de registro enviado. Neste procedimento, você pode adicionar usuários à lista. Como alternativa, você pode desativar a opção **Restringir acesso**, o que permite que os usuários se registrem no laboratório, desde que eles tenham o link de registro. 
2. Selecione **Adicionar usuários** na barra de ferramentas e, em seguida, selecione **Adicionar por endereços de email**. 

    ![Botão Adicionar usuários](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **Adicionar usuários**, insira endereços de email dos usuários em linhas separadas ou em uma única linha e separados por ponto e vírgula. 

    ![Adicionar endereços de email do usuário](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Clique em **Salvar**. Você vê os endereços de email de usuários e seus status (registrados ou não) na lista. 

    ![Lista de usuários](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-a-schedule-for-the-lab"></a>Definir uma agenda para o laboratório
Crie um evento agendado para o laboratório, de modo que as VMs no laboratório sejam iniciadas/paradas automaticamente em horários específicos. A cota de usuário especificada anteriormente é o tempo adicional atribuído a cada usuário fora desse horário agendado. 

1. Alterne para a página **Agendamentos** e selecione **Adicionar evento agendado** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página Agendamentos](../media/how-to-create-schedules/add-schedule-button.png)
2. Confirme se **Padrão** está selecionado para o **Tipo de evento**. Selecione **Iniciar apenas** para especificar apenas a hora de início das VMs. Selecione **Parar apenas** para especificar apenas a hora de término das VMs. 
7. Na seção **Repetir**, selecione a agenda atual. 

    ![Botão Adicionar agendamento na página Agendamentos](../media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **Repetir**, execute as seguintes etapas:
    1. Confirme se a opção **toda semana** está definida para o campo **Repetir**. 
    3. Especifique a **data de início**.
    4. Especifique a **hora de início** em que deseja iniciar as VMs.
    5. Especifique a **hora de término** em que as VMs devem ser desligadas. 
    6. Especifique o **fuso horário** para as horas de início e de término especificadas. 
    2. Selecione os dias nos quais deseja que a agenda entre em vigor. No exemplo a seguir, Segunda-Quinta foi selecionado. 
    8. Clique em **Salvar**. 

        ![Definir agenda de repetição](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página **Adicionar evento agendado**, em **Observações (opcional)** , insira uma descrição ou observações para o agendamento. 
4. Na página **Adicionar evento agendado**, selecione **Salvar**. 

    ![Agendamento semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-invitation-emails-to-students"></a>Enviar emails de convite para os alunos

1. Alterne para a exibição **Usuários**, caso ainda não esteja na página, e selecione **Convidar todos** na barra de ferramentas. 

    ![Selecionar alunos](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Na página **Enviar convite por email**, insira uma mensagem opcional e, em seguida, selecione **Enviar**. O email inclui automaticamente o link de registro. Obtenha esse link de registro selecionando **... (reticências)** na barra de ferramentas e **Link de registro**. 

    ![Enviar link de registro por email](../media/tutorial-setup-classroom-lab/send-email.png)
4. Você verá o status de **convite** na lista **Usuários**. O status deverá ser alterado para **Enviando** e, em seguida, para **Enviado em &lt;data&gt;** . 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um laboratório de sala de aula e configurou o laboratório. Para saber como um aluno pode acessar uma VM no laboratório usando o link de registro, vá para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Conectar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

