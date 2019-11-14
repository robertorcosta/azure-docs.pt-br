---
title: Criar uma VM do Azure gerenciada a partir de um VHD local generalizado
description: Carregar um VHD generalizado no Azure e usá-lo para criar novas VMs, no modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: d0995fed61d169cc173ca01767c2e48f4f798b0d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067430"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carregar um VHD generalizado e usá-lo para criar novas VMs no Azure

Este artigo orienta você a usar o PowerShell para carregar um VHD de uma máquina virtual generalizada no Microsoft Azure, crie uma imagem do VHD e crie uma nova VM dessa imagem. Você pode carregar um VHD exportado de uma ferramenta de virtualização local ou de outra nuvem. Usar [Discos Gerenciados](managed-disks-overview.md) para a nova VM simplifica o gerenciamento da VM e fornece maior disponibilidade quando a VM é colocada em um conjunto de disponibilidade. 

Para um exemplo de script, consulte [Script de exemplo para carregar um VHD no Azure e criar uma nova VM](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Antes de começar

- Antes de carregar qualquer VHD no Azure, você deve seguir as etapas em [Preparar um VHD ou VHDX do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Examine [Planejar a migração para Discos Gerenciados](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) antes de iniciar a migração para [Discos Gerenciados](managed-disks-overview.md).

 


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalize a VM de origem usando o Sysprep

O Sysprep remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre Sysprep, consulte a [Visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Se você planeja executar o Sysprep antes de carregar o VHD no Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Entre na máquina virtual Windows.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para % windir%\system32\sysprep e, em seguida, execute `sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está ativada.
4. Para **Opções de Desligamento**, selecione **Desligamento**.
5. Selecione **OK**.
   
    ![Inicie o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep for concluído, desligará a máquina virtual. Não reinicie a VM.


## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD na sua conta de armazenamento

Agora você pode carregar um VHD diretamente em um disco gerenciado. Para obter instruções, consulte [carregar um VHD no Azure usando Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Criar uma imagem gerenciada usando o VHD carregado 

Crie uma imagem gerenciada do seu disco de gerenciamento generalizado do sistema operacional. Substitua os valores a seguir com suas próprias informações.


Primeiro, defina alguns parâmetros:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Crie a imagem usando o VHD do SO generalizado.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Criar a VM

Agora que tem uma imagem, você pode criar uma ou mais VMs novas por meio da imagem. Este exemplo cria uma VM denominada *myVM* a partir de *myImage*, em *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Próximas etapas

Logue na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

