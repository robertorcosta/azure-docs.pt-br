---
title: Introdução ao Azure Lab Services
description: Este artigo descreve como começar a usar o Azure Lab Services.
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: 33e052931b0c3bd1bb1434b7eeefeed7a2a7ceab
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380116"
---
# <a name="get-started-with-lab-services"></a>Introdução aos serviços de laboratório 

Como aluno, você pode usar Azure Lab Services para acessar o software padrão do setor necessário para seus programas de estudo em máquinas virtuais (VM). 

Os professores precisam saber como ensinar os alunos a utilizar Azure Lab Services em suas instruções por meio de um hardware emitido por um aluno.

Este artigo fornece informações para ensinar a equipe sobre como acessar, gerenciar e ensinar os alunos a utilizar Azure Lab Services.

## <a name="overview"></a>Visão geral

O que é uma VM e como elas funcionam?

Uma VM (máquina virtual) é um ambiente virtual que atua como um computador virtual. As VMs têm seu próprio processador, memória e armazenamento. As VMs fornecem um substituto para uma máquina real e podem dar aos usuários acesso a sistemas operacionais e software sem a necessidade de tê-las em seu próprio dispositivo. O Azure Lab Services fornece uma ferramenta para que os alunos acessem e naveguem pelas VMs e para a equipe para gerenciar seus laboratórios de computador virtual. 

Para obter mais informações, consulte [criar e gerenciar laboratórios de sala de aula](how-to-manage-classroom-labs.md).

## <a name="lab-dashboards"></a>Painéis de laboratório

Painéis para laboratórios de sala de aula no Azure Lab Services fornecem um instantâneo de diferentes aspectos de um laboratório específico, incluindo informações de VM, número de VMs atribuídas e não atribuídas, número de usuários registrados e não registrados e informações sobre agendamentos de laboratório. 

> [!NOTE]
> Embora a maioria dos aspectos administrativos do painel e do [Azure Lab Services site](https://labs.azure.com/) fique visível para os professores, as permissões específicas para sua função podem afetar sua capacidade de modificar determinados critérios no painel. Se você encontrar um problema com sua configuração de laboratório específica, entre em contato com o administrador da CTE.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="o portal de Azure Lab Services":::

1. Navegue e entre no site do [Azure Lab Services](https://labs.azure.com/).
1. Selecione seu laboratório.
1. Você verá um **painel** no lado esquerdo da janela. Clique no **painel** e você verá vários blocos em seu painel.
1. Abaixo dos **custos &** bloco de cobrança, também há blocos para modelos, pools de máquinas virtuais, usuários e agendas, que permitem modificar aspectos e exibir mais detalhes sobre o laboratório da sala de aula.

    1. Modelo – descreve a data em que o modelo foi criado e a última publicação. 
    1. Pool de máquinas virtuais-número de VMs atribuídas e não atribuídas.
    1. Usuários-número de usuários registrados e usuários que foram adicionados ao laboratório, mas não registrados.
    1. Agendas – exibe eventos agendados futuros para o laboratório e um link para exibir mais eventos.

Para obter mais informações, consulte [usar o painel](use-dashboard.md).

## <a name="quota-hours"></a>Horas de cota

Os alunos podem acessar suas VMs a qualquer momento durante a hora da aula agendada sem afetar as horas de cota. As horas de cota são definidas para o semestre inteiro e determinam o número de horas que um aluno pode usar sua VM fora da hora da classe agendada regularmente.

8 horas por semana, redefinidas em domingo – não cumulativa.

Para obter mais informações, consulte [set quota](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="automatic-shut-down"></a>Desligamento automático

Para ajudar a manter os custos e poupar horas de cota dos alunos, os desligamentos automáticos serão habilitados para nossos laboratórios. Os desligamentos automáticos desativarão as VMs após um período de inatividade (sem entradas de mouse ou teclado). Os desligamentos automáticos funcionam em dois estágios, primeiro um aluno será desconectado da VM após um período de inatividade. Neste ponto, a VM ainda está **em execução** e os alunos podem se conectar. Após outro período de inatividade depois de desconectado, a VM será desligada.

Os desligamentos automáticos são uma ferramenta de economia de custos importante, no entanto, eles apresentam um desafio para alunos em relação ao salvamento de seu trabalho e à renderização de arquivos de projetos grandes. Se os alunos forem freqüentemente desconectados ou se as VMs estiverem desligadas muito rapidamente. Entre em contato com o administrador da CTE. 

Para obter mais informações, consulte [Configurar o desligamento automático de VMs para uma conta de laboratório](how-to-configure-lab-accounts.md).

## <a name="managing-virtual-machines"></a>Gerenciando máquinas virtuais

O gerenciamento do laboratório permite que os professores controlem itens como a capacidade do laboratório (o número de VMs disponíveis para alunos) e iniciem, interrompam ou redefinam as VMs manualmente. os professores também podem se conectar a VMs para experimentar a interface do aluno, acessar arquivos e solucionar problemas com software ou a própria VM.

A coisa mais importante a ser lembrada ao gerenciar nossas VMs é que, sempre que um computador estiver **em execução** , estamos incorrendo em custos, mesmo que ninguém esteja conectado à VM.

### <a name="manually-starting-vms"></a>Iniciando VMs manualmente

1. Na página **pool de máquinas virtuais** , você pode iniciar todas as VMs em um laboratório clicando no botão **Iniciar tudo** na parte superior da página.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Inicie suas VMs":::
1. VMs individuais podem ser iniciadas clicando-se na alternância de estado. 

    A alternância será lida a **partir do início** da VM e, em seguida, **executada** quando a VM for iniciada.
1. Você também pode selecionar um número de VMs usando as verificações à esquerda da coluna **nome** . 

    Depois de selecionar as VMs desejadas, clique no botão **Iniciar** na parte superior da tela.
1. Depois de iniciado, você pode clicar no botão **parar tudo** para interromper todas as VMs.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Pare suas VMs":::

### <a name="stopping-and-resetting-vms"></a>Interrompendo e redefinindo VMs

* Você pode parar VMs individuais clicando na alternância de estado.
* Você também pode interromper várias VMs usando as verificações e clicando no botão "parar" na parte superior da tela.

Se um aluno estiver enfrentando dificuldades para se conectar à VM ou se a VM precisar ser redefinida por qualquer outro motivo, você poderá usar a função redefinir.
1. Para redefinir uma ou mais VMs, selecione-as usando as verificações e, em seguida, clique no botão **Redefinir** na parte superior da página.
1. Na janela pop-up, clique em **Redefinir**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Redefinir sua VM":::

    > [!NOTE]
    > Ativar uma VM de aluno não afetará a cota do aluno. Cotas para usuários especifica o número de horas de laboratório disponíveis para o usuário fora da hora da classe agendada.

### <a name="connect-to-virtual-machines"></a>Conectar-se a máquinas virtuais

Os professores podem se conectar a uma VM de aluno desde que ela esteja ativada e o aluno não esteja conectado à VM. Conectando-se à VM, você poderá acessar arquivos locais na VM e ajudar os alunos a solucionar problemas.

1. Para se conectar à VM do aluno, passe o mouse sobre a VM na lista e clique no botão **conectar** . 
1. Em seguida, siga o guia de introdução para estudantes para Chromebooks, Macs ou PCs

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Botão Conectar-se à VM do aluno":::

## <a name="add-and-manage-lab-users"></a>Adicionar e gerenciar os usuários do laboratório

Os professores podem adicionar usuários de aluno a um laboratório e monitorar suas cotas de hora. 

### <a name="add-users-by-email-address"></a>Adicionar usuários por endereço de email

1. No [site do Azure Lab Services](https://labs.azure.com/) , clique em **usuários** no lado esquerdo da janela.
1. Na parte superior da janela, clique em **Adicionar usuários** e selecione **Adicionar por endereço de email**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-button.png" alt-text="O botão ' Adicionar usuários '":::
1. No painel **Adicionar usuários** que aparece à direita, insira os endereços de email dos alunos em linhas separadas ou em uma única linha, separados por ponto e vírgula.
1. Clique em **Salvar**.

    :::image type="content" source="./media/get-started-manage-labs/add-students.png" alt-text="Adicionar alunos ao seu laboratório":::
1. Sua lista de usuários agora será atualizada com emails, status, convite e horas de cota.

    Depois que os alunos forem registrados para um laboratório, seus nomes serão atualizados com o nome e o sobrenome do diretório MPS.

    > [!NOTE]
    > Mantenha a opção de restrição de acesso restrito ativada para os usuários. Isso significa que somente os usuários que você lista podem registrar com o laboratório usando o link de registro que você envia.

### <a name="add-users-using-a-spreadsheet"></a>Adicionar usuários usando uma planilha 

Você também pode adicionar usuários carregando um arquivo CSV que contém seus endereços de email.

1. No Microsoft Excel, crie um arquivo CSV que liste os endereços de email dos alunos em uma coluna.
1. No [site Azure Lab Services](https://labs.azure.com/), na parte superior da página **usuários** , clique no botão **Adicionar usuários** .
1. Selecione **carregar CSV**.
1. Selecione o arquivo CSV que contém os endereços de email dos alunos e clique em **abrir**.

    :::image type="content" source="./media/get-started-manage-labs/add-users-spreadsheet.png" alt-text="Adicionar usuários usando uma planilha":::
1. Os emails agora serão exibidos na janela à direita. Clique em **Salvar**.

    :::image type="content" source="./media/get-started-manage-labs/register-users.png" alt-text="Registrar usuários":::

### <a name="register-users"></a>Registrar usuários

Depois que os usuários tiverem sido adicionados ao laboratório, eles precisarão se registrar para acessar as VMs. Isso pode ser feito convidando usuários do portal de serviços Web do Azure, que enviará um email contendo o link de registro para o laboratório. Ou copiando e colando o link de registro em um email ou outra forma de comunicação com os alunos.

1. Na página **usuários** , selecione um aluno ou vários alunos na lista.

    Na linha do aluno que você selecionou, selecione o ícone de envelope na lista ou clique em **convidar** na parte superior da tela.

    :::image type="content" source="./media/get-started-manage-labs/send-invitation.png" alt-text="Enviar um convite":::
    
    Na janela **Enviar convite** por email, insira uma mensagem opcional (como um nome de usuário e uma senha) para alunos e clique em **Enviar**. 
    
    :::image type="content" source="./media/get-started-manage-labs/send-invitation-mail.png" alt-text="Enviar um convite por email":::

    Como alternativa, na página mesmos **usuários** , você pode clicar no botão **link de registro** na parte superior da tela. 

    :::image type="content" source="./media/get-started-manage-labs/registration-link.png" alt-text="Link de registro do usuário":::
    
    Copie o link de registro do campo de texto e cole-o em email ou em sua ferramenta de mensagens seguras preferida.  
    
    :::image type="content" source="./media/get-started-manage-labs/user-registration.png" alt-text="Enviar registro de usuário":::

Depois de ter convidado os usuários por meio do portal do Azure ou compartilhado o link, você poderá monitorar quais usuários registraram com êxito na página **usuários** na coluna **status** . 

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não pretende usar os recursos que criou neste guia de início rápido, exclua os recursos.

## <a name="next-steps"></a>Próximas etapas

[Configurar uma conta de laboratório](tutorial-setup-lab-account.md)