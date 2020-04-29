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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: fcf31fcc266358911612c25e0b73a0a9de696b1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77539001"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Criar e gerenciar um modelo de sala de aula no Azure Lab Services
Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure a máquina virtual do modelo para que ela seja configurada exatamente com o que você deseja fornecer aos usuários de laboratório. Você pode fornecer um nome e uma descrição do modelo exibido para os usuários do laboratório. Em seguida, publique o modelo para tornar as instâncias do modelo de VM disponíveis para os usuários do laboratório. Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo.

Este artigo descreve como criar e gerenciar uma máquina virtual de modelo em um laboratório de sala de aula dos Serviços de Laboratório do Azure. 

## <a name="set-or-update-template-title-and-description"></a>Definir ou atualizar modelo de título e descrição
Use as etapas a seguir para definir o título e descrição para a primeira vez e atualizá-los mais tarde. 

1. Na página **modelo** , insira o novo **título** para o laboratório.  
2. Insira a nova **Descrição** para o modelo. Quando você move o foco para fora da caixa de texto, ele é salvo automaticamente. 

    ![Nome do modelo e descrição](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Atualizar uma VM de modelo
Use as etapas a seguir para atualizar uma VM de modelo.  

1. Aguarde até que a VM de modelo seja iniciada e, em seguida, selecione **conectar ao modelo** na barra de ferramentas para se conectar à VM de modelo e siga as instruções. Se for um computador Windows, você verá uma opção para baixar o arquivo RDP. 
1. Quando você se conectar ao modelo e fizer alterações, ele não terá mais a mesma configuração que as máquinas virtuais publicadas pela última vez para os usuários. As alterações de modelo não serão refletidas nas máquinas virtuais existentes de seus usuários até que você publique novamente.

    ![Conectar à VM modelo](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Instale o software necessário para que os alunos façam o laboratório (por exemplo, Visual Studio, Gerenciador de Armazenamento do Azure etc.). 
1. Desconecte (feche sua sessão de área de trabalho remota) da VM modelo. 
1. **Pare** a VM do modelo selecionando **parar modelo**. 
1. Siga as etapas na próxima seção para **publicar** a VM de modelo atualizada. 

## <a name="publish-the-template-vm"></a>Publicar a VM modelo  
Nessa etapa, você publica a VM de modelo. Quando você publica a VM de modelo, o Azure Lab Services cria VMs no laboratório usando o modelo. Todas as máquinas virtuais têm a mesma configuração que o modelo.


1. Na página **Modelo**, selecione **Publicar** na barra de ferramentas. 

    ![Botão Publicar modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, você não pode cancelar a publicação. 
2. Na página **Publicar modelo**, insira o número de máquinas virtuais que deseja criar no laboratório e, em seguida, selecione **Publicar**. 

    ![Publicar modelo – número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Você verá o **status de publicação** do modelo na página. Esse processo pode levar até uma hora. 

    ![Publicar modelo – andamento](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Aguarde até que a publicação seja concluída e, em seguida, alterne para a página **Pool de máquinas virtuais** selecionando **Máquinas virtuais** no menu esquerdo ou selecionando o bloco **Máquinas virtuais**. Confirme que você vê cinco máquinas virtuais no estado **Não atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
