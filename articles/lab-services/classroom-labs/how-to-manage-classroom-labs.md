---
title: Gerenciar laboratórios de salas de aula no Azure Lab Services | Microsoft Docs
description: Saiba como criar e configurar um laboratório de sala de aula, exibir todos os laboratórios de sala de aula, compartilhar o link de registro com um usuário de laboratório, ou excluir um laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: cc937589b2fc4f394b44cf6890a352d770751d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502027"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gerenciar laboratórios de sala de aula no Azure Lab Services 
Este artigo descreve como criar e excluir um laboratório de sala de aula. Isso também mostra como exibir todos os laboratórios de sala de aula em uma conta de laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, você deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que você usou para criar uma conta de laboratório é adicionada automaticamente a essa função. Um proprietário de laboratório pode adicionar outros usuários à função de Criador de Laboratório seguindo as etapas neste artigo: [Adicionar um usuário à função de Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). O Internet Explorer 11 ainda não tem suporte. 
2. Selecione **Iniciar sessão** e insira suas credenciais. Selecione ou insira uma **ID de usuário** que é um membro da função **Criador de laboratório** na conta do laboratório e insira a senha. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Selecione **Novo laboratório**. 
    
    ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório. 
    2. Selecione o **tamanho das máquinas virtuais necessárias** para a classe. Para obter a lista de tamanhos disponíveis, consulte a seção [Tamanhos vm.](#vm-sizes) 
    3. Selecione a **imagem da máquina virtual** que você deseja usar para o laboratório de sala de aula. Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, veja [Habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).
    4. Revise o **preço total por hora** exibido na página. 
    6. Selecione **Salvar**.

        ![Janela Novo laboratório](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Você vê uma opção para selecionar um local para o seu laboratório se a conta do laboratório foi configurada para permitir que o criador do laboratório escolha a opção [de localização do laboratório.](allow-lab-creator-pick-lab-location.md) 
4. Na página **Credenciais da máquina virtual**, especifique as credenciais padrão para todas as VMs no laboratório.
    1. Especifique o **nome do usuário** para todas as VMs no laboratório.
    2. Especifique a **senha** do usuário. 

        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.
    3. Desativar **Use a mesma senha para todas as máquinas virtuais** se quiser que os alunos definam suas próprias senhas. Esta etapa é **opcional**. 

        Um professor pode optar por usar a mesma senha para todas as VMs do laboratório, ou permitir que os alunos definam senhas para suas VMs. Por padrão, esta configuração está habilitada para todas as imagens do Windows e Linux, exceto para o Ubuntu. Quando você seleciona **o Ubuntu** VM, essa configuração é desativada, de modo que os alunos serão solicitados a definir uma senha quando eles fizerem login pela primeira vez.  

        ![Janela Novo laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Em seguida, **selecione Next** na página **de credenciais da máquina virtual.** 
5. Na página de políticas do **Laboratório,** faça as seguintes etapas:
    1. Digite o número de horas atribuídas para cada usuário (**cota para cada usuário**) fora do horário programado para o laboratório. 
    2. Para o desligamento automático da opção **máquinas virtuais,** especifique se deseja que a VM seja desligada automaticamente quando o usuário se desconectar. Você também pode especificar quanto tempo a VM deve esperar para que o usuário se reconecte antes de ser automaticamente desligado.. Para obter mais informações, consulte [Ativar o desligamento automático de VMs na desconexão](how-to-enable-shutdown-disconnect.md).
    3. Em seguida, **selecione Concluir**. 

        ![Cota para cada usuário](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Você deverá ver a tela a seguir que mostra o status da criação da VM de modelo. A criação do modelo no laboratório leva até 20 minutos. 

    ![Status da criação da VM de modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **Modelo,** faça as seguintes etapas: Estas etapas são **opcionais** para o tutorial.

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

### <a name="vm-sizes"></a>Tamanhos de VM  

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequena | 2 | 3,5 GB | Este tamanho é mais adequado para linha de comando, abertura de navegador da Web, servidores web de baixo tráfego, bancos de dados pequenos a médios. |
| Médio | 4 | 7 GB | Este tamanho é mais adequado para bancos de dados relacionais, cache na memória e análises | 
| Média (virtualização aninhada) | 4 | 16 GB | Este tamanho é mais adequado para bancos de dados relacionais, cache na memória e análises. Esse tamanho também suporta virtualização aninhada. <p>Este tamanho pode ser usado em cenários onde cada aluno precisa de várias VMs. Os professores podem usar a virtualização aninhada para configurar algumas máquinas virtuais aninhadas de pequeno porte dentro da máquina virtual. </p> |
| grande | 8 | 32 GB | Este tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, grandes bancos de dados, grandes caches de memória. Esse tamanho também suporta virtualização aninhada |  
| Pequena GPU (Visualização) | 6 | 56 GB | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando frameworks como OpenGL e DirectX. | 
| GPU pequena (Computação) | 6 | 56 GB | Este tamanho é mais adequado para aplicações intensivas em computação e com uso intensivo de rede, como inteligência artificial e aplicações de aprendizagem profunda. | 
| GPU média (Visualização) | 12 | 112 GB | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando frameworks como OpenGL e DirectX. | 

> [!NOTE]
> O Azure Lab Services instala e configura automaticamente os drivers de GPU necessários para você quando você cria um laboratório com imagens de GPU.  

## <a name="view-all-classroom-labs"></a>Exibir todos os laboratórios de sala de aula
1. Navegue até [Portal do Azure Lab Services](https://labs.azure.com).
2. Selecione **Fazer login**. Selecione ou insira uma **ID de usuário** que é um membro da função **Criador de laboratório** na conta do laboratório e insira a senha. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Confirme se você vê todos os laboratórios na conta de laboratório selecionada. Na telha do laboratório, você vê o número de máquinas virtuais no laboratório e a cota para cada usuário (fora do horário programado).

    ![Todos os laboratórios](../media/how-to-manage-classroom-labs/all-labs.png)
3. Use a lista suspensa na parte superior para selecionar uma conta de laboratório diferente. Você verá laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Excluir um laboratório de sala de aula
1. No azulejo para o laboratório, selecione três pontos (...) no canto e, em seguida, **selecione Excluir**. 

    ![Botão Excluir](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na caixa de diálogo **Excluir laboratório,** **selecione Excluir** para continuar com a exclusão. 

## <a name="switch-to-another-classroom-lab"></a>Alternar para outro laboratório de sala de aula
Para alternar do atual laboratório de sala de aula para outro, selecione a lista suspensa de laboratórios na conta de laboratório na parte superior.

![Selecione o laboratório na lista suspensa na parte superior](../media/how-to-manage-classroom-labs/switch-lab.png)

Você também pode criar um novo laboratório usando o **novo laboratório** nesta lista de baixa. 

> [!NOTE]
> Você também pode usar o módulo Az.LabServices PowerShell (visualização) para gerenciar laboratórios. Para obter mais informações, consulte a página inicial do [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Para mudar para uma conta de laboratório diferente, selecione o drop-down ao lado da conta do laboratório e selecione a outra conta do laboratório. 

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)

