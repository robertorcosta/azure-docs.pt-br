---
title: Desanexar um disco de dados de uma VM do Windows-Azure
description: Desanexe um disco de dados de uma máquina virtual no Azure usando o modelo de implantação do Resource Manager.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 301f3abd26f702f3f29c8833c835ba7d0e41bcaf
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834598"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual Windows

Quando não precisar mais de um disco de dados conectado a uma máquina virtual, você poderá desanexá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento.

> [!WARNING]
> Se você desanexar um disco, ele não será excluído automaticamente. Se você se inscreveu para o armazenamento Premium, você continuará incorrendo em encargos de armazenamento para o disco. Para obter mais informações, consulte [Preços e cobrança ao usar o Armazenamento Premium](disks-types.md#billing).

Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.

 

## <a name="detach-a-data-disk-using-powershell"></a>Desanexar um disco de dados usando o PowerShell

É possível remover um disco de dados *hot* usando o PowerShell, mas certifique-se de que nada esteja usando ativamente o disco antes de desanexá-lo da VM.

Neste exemplo, removemos o disco nomeado **myDisk** da VM **myVM** no grupo de recursos **myResourceGroup**. Primeiro, remova o disco usando o cmdlet [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk). Em seguida, atualize o estado da máquina virtual, usando o cmdlet [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) para concluir o processo de remoção do disco de dados.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

O disco permanecerá no armazenamento, mas não estará mais conectado a uma máquina virtual.

## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados usando o portal

Você pode *remover* um disco de dados, mas certifique-se de que nada esteja usando o disco ativamente antes de desanexá-lo da VM.

1. No menu à esquerda, selecione **Máquinas Virtuais**.
1. Selecione a máquina virtual que tem o disco de dados que você deseja desanexar.
1. Em **Configurações**, selecione **Discos**.
1. Na parte superior do painel **Discos**, selecione **Editar**.
1. No painel **discos** , na extrema direita do disco de dados que você deseja desanexar, selecione **desanexar**.
1. Selecione **salvar** na parte superior da página para salvar as alterações.

O disco permanecerá no armazenamento, mas não estará mais conectado a uma máquina virtual.

## <a name="next-steps"></a>Próximos passos

Se você quiser reutilizar o disco de dados, você poderá simplesmente [anexá-lo a outra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
