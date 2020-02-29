---
title: Anexar ou desanexar um disco de dados a uma máquina virtual no Azure DevTest Labs
description: Saiba como anexar ou desanexar um disco de dados a uma máquina virtual no Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2020
ms.author: spelluru
ms.openlocfilehash: 3f18425408e6526904db85eae1c3a4db41d11a58
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198746"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Anexar ou desanexar um disco de dados a uma máquina virtual no Azure DevTest Labs
O [Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) gerencia as contas de armazenamento associadas aos discos de dados da máquina virtual. Um usuário anexa um novo disco de dados a uma VM, especifica o tipo e o tamanho do disco que é necessário e o Azure cria e gerencia o disco automaticamente. O disco de dados pode então ser desanexado da VM e reanexado posteriormente à mesma VM ou anexado a outra VM que pertença ao mesmo usuário.

Essa funcionalidade é útil para gerenciar o armazenamento ou software do lado de fora de cada máquina virtual individual. Se o armazenamento ou software já existir dentro de um disco de dados, ele poderá ser facilmente anexado, desanexado e reanexado a qualquer VM que pertença ao usuário que possui o disco de dados.

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Antes de anexar um disco de dados a uma VM, leia estas dicas:

- O tamanho da VM controla quantos discos de dados você pode anexar. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Você pode anexar apenas um disco de dados a uma VM que esteja em execução. Verifique se a VM está em execução antes de tentar anexar um disco de dados.

### <a name="attach-a-new-disk"></a>Anexar um novo disco
Siga estas etapas para criar e anexar um novo disco de dados gerenciado a uma VM no Azure DevTest Labs.

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório desejado. 
1. Na lista de **Minhas máquinas virtuais**, selecione uma VM em execução.
1. No menu à esquerda, selecione **Discos**.
1. Escolha **Anexar novo** para criar um novo disco de dados e anexá-lo à VM.

    ![Anexar novo disco de dados a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Complete o painel **Anexar novo disco** digitando o nome, o tipo e o tamanho de um disco de dados.

    ![Preencher o formulário "anexar novo disco"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Selecione **OK**.

Após alguns instantes, o novo disco de dados é criado e anexado à VM e aparece na lista de **discos de dados** para essa VM.

### <a name="attach-an-existing-disk"></a>Anexar um disco existente
Siga estas etapas para reanexar um disco de dados existente disponível a uma VM em execução. 

1. Selecione uma VM em execução à qual você deseja reanexar um disco de dados.
1. No menu à esquerda, selecione **Discos**.
1. Selecione **Anexar existente** para anexar um disco de dados disponível à VM.

    ![Anexar disco de dados existente a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. No painel **Anexar disco existente**, selecione OK.

    ![Anexar disco de dados existente a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Após alguns instantes, o disco de dados é anexado à VM e aparece na lista de **discos de dados** para essa VM.

## <a name="detach-a-data-disk"></a>Desanexar um disco de dados
Quando não precisar mais de um disco de dados que está conectado a uma VM, você poderá desanexá-lo facilmente. A desanexação remove o disco da VM, mas o mantém no armazenamento para uso posterior.

Se desejar usar os dados existentes no disco novamente, você poderá reanexá-lo à mesma máquina virtual ou a qualquer outra.

### <a name="detach-from-the-vms-management-pane"></a>Desanexar usando o painel de gerenciamento da VM
1. Na lista de máquinas virtuais, selecione uma VM que tenha um disco de dados anexado.
1. No menu à esquerda, selecione **Discos**.
1. Na lista de **discos de dados**, selecione o disco de dados que você deseja desanexar.

    ![Selecionar discos de dados para uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Selecione **Desanexar** na parte superior do painel de detalhes do disco.

    ![Desanexar um disco de dados](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Selecione **Sim** para confirmar que deseja desanexar o disco de dados.

O disco é desanexado e está disponível para ser anexado a outra VM. 
### <a name="detach-from-the-labs-main-pane"></a>Desanexar usando o painel principal do laboratório
1. No painel de principal do laboratório, selecione **Meus discos de dados**.
1. Clique com o botão direito do mouse no disco de dados que você deseja desanexar – ou selecione suas reticências ( **...** ) – e escolha **desanexar**.

    ![Desanexar um disco de dados](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Selecione **Sim** para confirmar que deseja desanexá-lo.

   > [!NOTE]
   > Se um disco de dados já estiver desanexado, você poderá optar por removê-lo da lista de discos de dados disponíveis selecionando **Excluir**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Upgrade de um disco de dados não gerenciado
Caso tenha uma VM existente que usa discos de dados não gerenciados, você poderá converter facilmente a VM para usar discos gerenciados. Esse processo converte o disco do sistema operacional e os discos de dados anexados.

Para fazer upgrade de um disco de dados não gerenciado, siga as etapas descritas neste artigo para [desanexar o disco de dados](#detach-a-data-disk) de uma VM não gerenciada. Em seguida, [reanexe o disco](#attach-an-existing-disk) a uma VM gerenciada para fazer upgrade automaticamente do disco de dados de não gerenciado para gerenciado.

## <a name="next-steps"></a>Próximas etapas
Saiba como gerenciar discos de dados para [máquinas virtuais requisitáveis](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

