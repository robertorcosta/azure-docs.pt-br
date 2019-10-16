---
title: Gerenciar um modelo de um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Aprenda a criar e gerenciar um modelo de laboratório de sala de aula nos Serviços de Laboratório do Azure.
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
ms.openlocfilehash: f0fc1e143ce7d271d5faaa8dda0eb40cdfc9e006
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332790"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Criar e gerenciar um modelo de sala de aula no Azure Lab Services
Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure a máquina virtual do modelo para que ela seja configurada exatamente com o que você deseja fornecer aos usuários de laboratório. Você pode fornecer um nome e uma descrição do modelo exibido para os usuários do laboratório. Em seguida, publique o modelo para tornar as instâncias do modelo de VM disponíveis para os usuários do laboratório. Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo.

Este artigo descreve como criar e gerenciar uma máquina virtual de modelo em um laboratório de sala de aula dos Serviços de Laboratório do Azure. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publicar um modelo ao criar um laboratório de sala de aula
Para saber como publicar um modelo durante a criação de um laboratório de sala de aula, consulte [criar um laboratório de sala de aula](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>Definir ou atualizar modelo de título e descrição
Use as etapas a seguir para definir o título e descrição para a primeira vez e atualizá-los mais tarde. 

1. Na página **modelo** , insira o novo **título** para o laboratório.  
2. Insira a nova **Descrição** para o modelo. Quando você move o foco para fora da caixa de texto, ele é salvo automaticamente. 

    ![Nome do modelo e descrição](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Atualizar uma VM de modelo
Use as etapas a seguir para atualizar uma VM de modelo.  

1. Na página **modelo** , selecione **Personalizar modelo** na barra de ferramentas. 

    ![Botão Personalizar modelo](../media/how-to-create-manage-template/customize-template-button.png)
2. Na caixa de diálogo **Personalizar modelo** , selecione **continuar**. Depois de iniciar o modelo e fazer alterações, ele não terá mais a mesma configuração que as máquinas virtuais publicadas pela última vez para os usuários. As alterações de modelo não serão refletidas nas máquinas virtuais existentes de seus usuários até que você publique novamente.

    ![Caixa de diálogo Personalizar](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Selecione o botão **conectar ao modelo** na barra de ferramentas para se conectar à VM do modelo e siga as instruções. Se for um computador Windows, você verá uma opção para baixar o arquivo RDP. 

    ![Conectar à VM modelo](../media/how-to-create-manage-template/connect-template-vm.png)
1. Instale o software necessário para que os alunos façam o laboratório (por exemplo, Visual Studio, Gerenciador de Armazenamento do Azure etc.). 
2. Desconecte (feche sua sessão de área de trabalho remota) da VM modelo. 
3. **Pare** a VM do modelo selecionando **parar modelo**. 
4. Siga as etapas na próxima seção para **publicar** a VM de modelo atualizada. 

## <a name="publish-the-template-vm"></a>Publicar a VM modelo  
Se você não publique o modelo ao criar o laboratório, você pode publicá-lo mais tarde. Antes de publicar, você talvez queira se conectar ao modelo de VM e atualizá-lo com qualquer software. Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas nesse processo são o número de VMs que você especificou quando a publicou pela primeira vez ou o que você especificou na página do pool de máquinas virtuais. Todas as máquinas virtuais têm a mesma configuração que o modelo. 

1. Na página **modelo** , selecione **publicar** na barra de ferramentas. 
1. Sobre o **publicar o modelo** caixa de mensagem, examine a mensagem e selecione **publicar**. Esse processo poderá levar algum tempo, dependendo de quantas VMs estão sendo criadas.

    ![Botão Publicar](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Depois que um modelo é publicado, ele não pode ser cancelado. No entanto você poderá republicar o modelo. 
4. Você pode ver o status do processo de publicação na página de modelo. Aguarde até o status do modelo ser alterado para **Publicado**. 

    ![Status de publicação](../media/how-to-create-manage-template/publish-status.png)
1. Alterne para a página **Máquinas virtuais** e confirme que você vê cinco máquinas virtuais no estado **Não Atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. Aguarde até que as máquinas virtuais sejam criadas. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Próximos passos
Confira os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
