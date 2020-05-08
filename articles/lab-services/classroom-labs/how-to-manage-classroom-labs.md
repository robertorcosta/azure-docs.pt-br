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
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: ed1c2f14ca5a791743a95cc50e5af0c29c74fefe
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983305"
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
    2. Selecione o **tamanho das máquinas virtuais** que você precisa para a classe. Para obter a lista de tamanhos disponíveis, consulte a seção [tamanhos de VM](#vm-sizes) . 
    3. Selecione a **imagem de máquina virtual** que você deseja usar para o laboratório de sala de aula. Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, veja [Habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).

        Se você tiver entrado usando credenciais do proprietário da conta do laboratório, verá uma opção para habilitar mais imagens para o laboratório. Para obter mais informações, consulte [Habilitar imagens no momento da criação do laboratório](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    4. Examine o **preço total por hora** exibido na página. 
    6. Selecione **Salvar**.

        ![Janela Novo laboratório](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Você verá uma opção para selecionar um local para o laboratório se a conta do laboratório tiver sido configurada para [permitir que o criador do laboratório escolha](allow-lab-creator-pick-lab-location.md) a opção local do laboratório. 
4. Na página **Credenciais da máquina virtual**, especifique as credenciais padrão para todas as VMs no laboratório.
    1. Especifique o **nome do usuário** para todas as VMs no laboratório.
    2. Especifique a **senha** do usuário. 

        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.
    3. Desabilitar a opção **usar a mesma senha para todas as máquinas virtuais** se desejar que os alunos definam suas próprias senhas. Essa etapa é **opcional**. 

        Um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, essa configuração é habilitada para todas as imagens do Windows e do Linux, exceto para Ubuntu. Quando você seleciona VM **Ubuntu** , essa configuração é desabilitada, portanto, os alunos serão solicitados a definir uma senha quando entrarem pela primeira vez.  

        ![Janela Novo laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Em seguida, selecione **Avançar** na página **credenciais da máquina virtual** . 
5. Na página **políticas de laboratório** , execute as seguintes etapas:
    1. Insira o número de horas alocadas para cada usuário (**cota para cada usuário**) fora do horário agendado para o laboratório. 
    2. Para a opção **desligamento automático de máquinas virtuais** , especifique se deseja que a VM seja desligada automaticamente quando o usuário se desconectar. Você também pode especificar quanto tempo a VM deve aguardar até que o usuário se reconecte antes de desligar automaticamente. Para obter mais informações, consulte [habilitar o desligamento automático de VMs na desconexão](how-to-enable-shutdown-disconnect.md).
    3. Em seguida, selecione **concluir**. 

        ![Cota para cada usuário](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Você deverá ver a tela a seguir que mostra o status da criação da VM de modelo. A criação do modelo no laboratório leva até 20 minutos. 

    ![Status da criação da VM de modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **modelo** , execute as seguintes etapas: essas etapas são **opcionais** para o tutorial.

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
| Pequena | 2 | 3,5 GB | Esse tamanho é mais adequado para linha de comando, abertura de navegador da Web, servidores Web de tráfego baixo, bancos de dados pequenos a médios. |
| Médio | 4 | 7 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise | 
| Médio (virtualização aninhada) | 4 | 16 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise. Esse tamanho também dá suporte à virtualização aninhada. <p>Esse tamanho pode ser usado em cenários em que cada aluno precisa de várias VMs. Os professores podem usar a virtualização aninhada para configurar algumas máquinas virtuais aninhadas de pequeno tamanho dentro da máquina virtual. </p> |
| GPU pequena (computação) | 6 | 56 GB | Esse tamanho é mais adequado para aplicativos com uso intensivo de computação e rede, como inteligência artificial e aplicativos de aprendizado profundo. | 
| GPU pequena (visualização) | 6 | 56 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 
| grande | 8 | 16 GB | Esse tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grande. |
| Grande (virtualização aninhada) | 8 | 32 GB | Esse tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grande. Esse tamanho também dá suporte à virtualização aninhada. |  
| GPU média (visualização) | 12 | 112 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 

> [!NOTE]
> Azure Lab Services instala e configura automaticamente os drivers de GPU necessários para você quando você cria um laboratório com imagens de GPU.  

## <a name="view-all-classroom-labs"></a>Exibir todos os laboratórios de sala de aula
1. Navegue até [Portal do Azure Lab Services](https://labs.azure.com).
2. Escolha **Entrar**. Selecione ou insira uma **ID de usuário** que é um membro da função **Criador de laboratório** na conta do laboratório e insira a senha. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
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

> [!NOTE]
> Você também pode usar o módulo do PowerShell AZ. LabServices (versão prévia) para gerenciar laboratórios. Para obter mais informações, consulte [AZ. LabServices Home Page no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Para alternar para uma conta de laboratório diferente, selecione a lista suspensa ao lado da conta de laboratório e selecione a outra conta de laboratório. 

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)

