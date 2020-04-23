---
title: Crie uma imagem gerenciada no Azure
description: Crie uma imagem gerenciada de uma VM ou um VHD generalizado no Azure. Imagens podem ser usadas para criar várias VMs que usam discos gerenciados.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 258bddec85e4ab182ff0b07c49cdc93f92264f95
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084457"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Criar uma imagem gerenciada de uma VM generalizada no Azure

Um recurso de imagem gerenciada pode ser criado de uma VM (máquina virtual) generalizada que é armazenada como um disco gerenciado ou um disco não gerenciado em uma conta de armazenamento. Em seguida, a imagem pode ser usada para criar várias VMs. Para obter informações de como as imagens gerenciadas são cobradas, confira [Preços do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM Windows usando Sysprep

O Sysprep remove todas as informações pessoais e de segurança da conta e prepara a máquina para ser usada como uma imagem. Para obter informações sobre o Sysprep, confira [Visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [o suporte do Sysprep para funções de servidor](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) e [cenários não suportados](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Depois que o Sysprep for executado em uma VM, essa VM será considerada *generalizada* e não poderá ser reiniciada. O processo de generalização de uma VM não é reversível. Se você precisar manter o funcionamento da VM original, crie uma [cópia da VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) e generalize a cópia. 
>
> Se você planeja executar o Sysprep antes de carregar o VHD (disco rígido virtual) no Azure pela primeira vez, [prepare a VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Para generalizar a VM do Windows, siga estas etapas:

1. Entre na VM do Windows.
   
2. Abra uma janela de Prompt de comando como administrador. Mude para o diretório para %windir%\system32\sysprep e, em seguida, execute `sysprep.exe`.
   
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na OOBE (configuração inicial pelo usuário) do sistema** e marque a caixa de seleção **Generalizar**.
   
4. Para **Opções de Desligamento**, selecione **Desligar**.
   
5. Selecione **OK**.
   
    ![Inicie o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Quando o Sysprep for concluído, ele desligará a VM. Não reinicie a VM.

> [!TIP]
> **Opcional** Use [o DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) para otimizar sua imagem e reduzir o primeiro tempo de inicialização da VM.
>
> Para otimizar sua imagem, monte seu VHD clicando duas vezes nele no Windows `/optimize-image` explorer e, em seguida, execute o DISM com o parâmetro.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Onde D: é o caminho do VHD montado.
>
> A `DISM /optimize-image` execução deve ser a última modificação que você faz ao seu VHD. Se você fizer qualquer alteração no seu VHD antes `DISM /optimize-image` da implantação, você terá que executar novamente.

## <a name="create-a-managed-image-in-the-portal"></a>Criação de uma imagem gerenciada no portal 

1. Vá até o [portal Azure](https://portal.azure.com) para gerenciar a imagem da VM. Pesquise por **Máquinas virtuais** e selecione essa opção.

2. Selecione sua VM na lista.

3. Na página **Máquina virtual** da VM, no menu superior, selecione **Capturar**.

   A página **Criar imagem** será exibida.

4. Para **Nome**, aceite o nome já preenchido ou insira um nome que você deseje usar para a imagem.

5. Para **o grupo Recurso,** selecione **Criar novo** e digite um nome ou selecione um grupo de recursos para usar na lista de baixa.

6. Se você quiser excluir a VM de origem depois que a imagem foi criada, selecione **Excluir automaticamente esta máquina virtual após criar a imagem**.

7. Se você quiser a capacidade de usar a imagem em qualquer [zona de disponibilidade](../../availability-zones/az-overview.md), selecione **Habilitado** para **Resiliência de zona**.

8. Selecione **Criar** para criar a imagem.

Depois que a imagem for criada, você poderá encontrá-la como um recurso de **imagem** na lista de recursos no grupo de recursos.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Criar uma imagem de uma VM usando o Powershell

 

Criar uma imagem diretamente da VM garante que a imagem inclua todos os discos associados à VM, incluindo o disco do sistema operacional e os discos de dados. Este exemplo mostra como criar uma imagem gerenciada de uma VM que usa discos gerenciados.

Antes de começar, certifique-se de ter a versão mais recente do módulo Azure PowerShell. Para localizar a versão, execute `Get-Module -ListAvailable Az` no PowerShell. Se você precisar atualizar, confira [Instalar o Azure PowerShell no Windows com o PowerShellGet](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, execute `Connect-AzAccount` para criar uma conexão com o Azure.


> [!NOTE]
> Se você quiser armazenar a imagem no armazenamento com redundância de zona, você precisará criá-la em uma região com suporte para [zonas de disponibilidade](../../availability-zones/az-overview.md) e incluir o parâmetro `-ZoneResilient` na configuração da imagem (comando `New-AzImageConfig`).

Para criar uma imagem de VM, siga estas etapas:

1. Defina algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Verifique se a VM foi desalocada.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Defina o status da máquina virtual como **Generalizado**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Obtenha a máquina virtual. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Crie a configuração de imagem.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Crie a imagem.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Crie uma imagem de um disco gerenciado usando o Powershell

Se você quiser criar uma imagem somente do disco do sistema operacional, especifique a ID do disco gerenciado como o disco do sistema operacional:

    
1. Defina algumas variáveis. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Obtenha a VM.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Obtenha a ID do disco gerenciado.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Crie a configuração de imagem.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Crie a imagem.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Crie uma imagem de um instantâneo usando o Powershell

Você pode criar uma imagem gerenciada usando um instantâneo de uma VM generalizada seguindo estas etapas:

    
1. Defina algumas variáveis. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Crie o instantâneo.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Crie a configuração de imagem.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Crie a imagem.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Crie uma imagem de uma VM que usa uma conta de armazenamento

Para criar uma imagem gerenciada de uma VM que não usa discos gerenciados, você precisa do URI do OS VHD na conta de armazenamento, no seguinte formato: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*. Neste exemplo, o VHD está em *mystorageaccount*, em um contêiner denominado *vhdcontainer*, e o nome do arquivo do VHD é *vhdfilename.vhd*.


1.  Defina algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Pare/desaloque a VM.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque a VM como generalizada.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Crie a imagem usando o VHD do sistema operacional generalizado.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Próximas etapas
- [Crie uma VM a partir de uma imagem gerenciada](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

