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
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 85a3a9f7afac8250b225d42462f6b29042e34a2a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330433"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gerenciar laboratórios de sala de aula no Azure Lab Services 
Este artigo descreve como criar e excluir um laboratório de sala de aula. Isso também mostra como exibir todos os laboratórios de sala de aula em uma conta de laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, você deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que você usou para criar uma conta de laboratório é adicionada automaticamente a essa função. Um proprietário de laboratório pode adicionar outros usuários à função de Criador de Laboratório seguindo as etapas neste artigo: [Adicionar um usuário à função de Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Crie um laboratório de sala de aula

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). Observe que ainda não há suporte para o Internet Explorer 11. 
2. Selecione **Iniciar sessão** e insira suas credenciais. Selecione ou insira uma **ID de usuário** que é um membro da função **Criador de laboratório** na conta do laboratório e insira a senha. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Selecione **novo laboratório**. 
    
    ![Crie um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório. 
    2. Selecione o **tamanho das máquinas virtuais** que você precisa para a classe. Para obter a lista de tamanhos disponíveis, consulte a seção [tamanhos de VM](#vm-sizes) . 
    3. Selecione a **imagem de máquina virtual** que você deseja usar para o laboratório de sala de aula. Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, veja [Habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).
    4. Examine o **preço total por hora** exibido na página. 
    6. Clique em **Salvar**.

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **credenciais da máquina virtual** , especifique as credenciais padrão para todas as VMs no laboratório.
    1. Especifique o **nome do usuário** para todas as VMs no laboratório.
    2. Especifique a **senha** do usuário. 

        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.
    3. Desabilitar a opção **usar a mesma senha para todas as máquinas virtuais** se desejar que os alunos definam suas próprias senhas. Esta etapa é **opcional**. 

        Um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, essa configuração é habilitada para todas as imagens do Windows e do Linux, exceto para Ubuntu. Quando você seleciona VM **Ubuntu** , essa configuração é desabilitada, portanto, os alunos serão solicitados a definir uma senha quando entrarem pela primeira vez.  

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.    
    4. Em seguida, selecione **Avançar** na página **credenciais da máquina virtual** . 
5. Na página **políticas de laboratório** , insira o número de horas alocadas para cada usuário (**cota para cada usuário**) fora da hora agendada para o laboratório e selecione **concluir**. 

    ![Cota para cada usuário](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Você deve ver a tela a seguir que mostra o status da criação da VM do modelo. A criação do modelo no laboratório leva até 20 minutos. 

    ![Status da criação da VM do modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **modelo** , execute as seguintes etapas: essas etapas são **opcionais** para o tutorial.

    2. Conecte-se à VM modelo selecionando **Conectar**. Se for uma VM de modelo do Linux, você escolherá se deseja se conectar usando SSH ou RDP (se RDP estiver habilitado).
    1. Selecione **Redefinição de senha** para redefinir a senha da VM. 
    1. Instale e configure software em sua VM modelo. 
    1. **Pare** a VM.  
    1. Insira uma **descrição** do modelo
10. Na página **modelo** , selecione **publicar** na barra de ferramentas. 

    ![Botão Publicar modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, você não pode cancelar a publicação. 
8. Na página **Publicar modelo** , insira o número de máquinas virtuais que você deseja criar no laboratório e, em seguida, selecione **publicar**. 

    ![Publicar modelo-número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Você verá o **status da publicação** do modelo na página. Esse processo pode levar até uma hora. 

    ![Publicar modelo – andamento](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Alterne para a página do **pool de máquinas virtuais** selecionando máquinas virtuais no menu à esquerda ou selecionando máquinas virtuais bloco. Confirme que você vê cinco máquinas virtuais no estado **Não atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Você executa as seguintes tarefas nesta página (não execute estas etapas para o tutorial. Estas etapas são apenas para suas informações.): 
    
    1. Para alterar a capacidade do laboratório (número de VMs no laboratório), selecione **capacidade do laboratório** na barra de ferramentas.
    2. Para iniciar todas as VMs de uma só vez, selecione **Iniciar tudo** na barra de ferramentas. 
    3. Para iniciar uma VM específica, selecione a seta para baixo no **status**e, em seguida, selecione **Iniciar**. Você também pode iniciar uma VM selecionando uma VM na primeira coluna e, em seguida, selecionando **Iniciar** na barra de ferramentas.                

### <a name="vm-sizes"></a>Tamanhos de VM  

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequena | 2 | 3,5 GB | Esse tamanho é mais adequado para linha de comando, abertura de navegador da Web, servidores Web de tráfego baixo, bancos de dados pequenos a médios. |
| Média | 4 | 7 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise | 
| Médio (virtualização aninhada) | 4 | 16 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise. Esse tamanho também dá suporte à virtualização aninhada. <p>Esse tamanho pode ser usado em cenários em que cada aluno precisa de várias VMs. Os professores podem usar a virtualização aninhada para configurar algumas máquinas virtuais aninhadas de pequeno tamanho dentro da máquina virtual. </p> |
| Grande | 8 | 32 GB | Esse tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grande. Esse tamanho também dá suporte à virtualização aninhada |  
| GPU pequena (visualização) | 6 | 56 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 
| GPU pequena (computação) | 6 | 56 GB | Esse tamanho é mais adequado para aplicativos com uso intensivo de computação e rede, como inteligência artificial e aplicativos de aprendizado profundo. | 
| GPU média (visualização) | 12 | 112 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 

## <a name="view-all-classroom-labs"></a>Exibir todos os laboratórios de sala de aula
1. Navegue até [Portal do Azure Lab Services](https://labs.azure.com).
2. Selecione **Entrar**. Selecione ou insira uma **ID de usuário** que é um membro da função **Criador de laboratório** na conta do laboratório e insira a senha. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Confirme se você vê todos os laboratórios na conta de laboratório selecionada. No bloco do laboratório, você verá o número de máquinas virtuais no laboratório e a cota de cada usuário (fora da hora agendada).

    ![Todos os laboratórios](../media/how-to-manage-classroom-labs/all-labs.png)
3. Use a lista suspensa na parte superior para selecionar uma conta de laboratório diferente. Você verá laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Excluir um laboratório de sala de aula
1. No bloco do laboratório, selecione três pontos (...) no canto e, em seguida, selecione **excluir**. 

    ![Botão Excluir](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na caixa de diálogo **excluir laboratório** , selecione **excluir** para continuar com a exclusão. 

## <a name="switch-to-another-classroom-lab"></a>Alternar para outro laboratório de sala de aula
Para alternar do atual laboratório de sala de aula para outro, selecione a lista suspensa de laboratórios na conta de laboratório na parte superior.

![Selecione o laboratório na lista suspensa na parte superior](../media/how-to-manage-classroom-labs/switch-lab.png)

Você também pode criar um novo laboratório usando o **novo laboratório** nesta lista suspensa. 


## <a name="next-steps"></a>Próximos passos
Confira os seguintes artigos:

- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)

