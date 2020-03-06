---
title: Excluir um laboratório ou VM em um laboratório no Azure DevTest Labs
description: Este artigo mostra como excluir um laboratório ou excluir uma VM em um laboratório usando o portal do Azure (Azure DevTest Labs).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 28fb464714f464a4c0a8f5eaf304dcdd5d603c90
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381019"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Excluir um laboratório ou VM em um laboratório no Azure DevTest Labs
Este artigo mostra como excluir um laboratório ou uma VM em um laboratório.

## <a name="delete-a-lab"></a>Excluir um laboratório
Quando você exclui uma instância do DevTest Labs de um grupo de recursos, o serviço DevTest Labs executa as seguintes ações: 

- Todos os recursos que foram criados automaticamente no momento da criação do laboratório são excluídos automaticamente. O grupo de recursos em si não é excluído. Se você tivesse manualmente criado todos os recursos nesse grupo de recursos, o serviço não irá excluí-los. 
- Todas as VMs nos grupos de laboratório e recursos associados a essas VMs são automaticamente excluídas. Quando você cria uma VM em um laboratório, o serviço cria recursos (disco, interface de rede, endereço IP público, etc.) para a VM em um grupo de recursos separado. No entanto, se você criar manualmente todos os recursos adicionais nesses grupos de recursos, o serviço DevTest Labs não excluirá esses recursos e o grupo de recursos. 

Para excluir um laboratório, realize as seguintes ações: 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** no menu à esquerda, selecione **DevTest Labs** para o tipo de serviço e selecione o laboratório.

    ![Selecione seu laboratório](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Na página **DevTest Lab**, selecione **Excluir** na barra de ferramentas. 

    ![Botão Excluir](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Na página **Confirmação**, insira o **nome** do seu laboratório e selecione **Excluir**. 

    ![Confirmar](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Para ver o status da operação, selecione o ícone **Notificações** (sino). 

    ![Notificações](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Excluir uma VM em um laboratório
Se eu excluir uma VM em um laboratório, alguns dos recursos (não todos) que foram criados no momento da criação do laboratório são excluídos. Os recursos a seguir não são excluídos: 

-   Crie um cofre de chaves no novo grupo de recursos
-   Disponibilidade definida, balanceador de carga, endereço IP público no grupo de recursos VM. Esses recursos são compartilhados por várias VMs em um grupo de recursos. 

Máquina virtual, interface de rede e disco associado à VM são excluídos. 

Para excluir um laboratório, realize as seguintes ações: 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** no menu à esquerda, selecione **DevTest Labs** para o tipo de serviço e selecione o laboratório.

    ![Selecione seu laboratório](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Selecione **... (reticências)**  para a VM na lista de VMs e selecione **Excluir**. 

    ![Exclua a VM no menu](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. Na caixa de diálogo **Confirmação**, selecione **Ok**. 
5. Para ver o status da operação, selecione o ícone **Notificações** (sino). 

Para excluir uma VM a partir de **página de Máquina Virtual**, selecione **Excluir** na barra de ferramentas, conforme mostrado na imagem a seguir:

![Excluir VM de página VM](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Próximas etapas
Se você quiser criar um laboratório, consulte os seguintes artigos: 

- [Criar um laboratório](devtest-lab-create-lab.md)
- [Adicionar uma VM ao laboratório](devtest-lab-add-vm.md)