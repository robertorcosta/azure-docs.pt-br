---
title: Converta o armazenamento de discos gerenciados entre ssd padrão e premium
description: Como converter discos gerenciados do Azure de Padrão para Premium ou Premium para Standard usando o Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720938"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Atualizar o tipo de armazenamento de um disco gerenciado

Existem quatro tipos de discos gerenciados pelo Azure: SSDs ultra (visualização) azure, SSD premium, SSD padrão e HDD padrão. Você pode alternar entre os três tipos de disco GA (SSD premium, SSD padrão e HDD padrão) com base nas suas necessidades de desempenho. Você ainda não é capaz de mudar de ou para um Ultra SSD, você deve implantar um novo.

Essa funcionalidade não é suportada para discos não gerenciados. Mas você pode facilmente [converter um disco não gerenciado em um disco gerenciado](convert-unmanaged-to-managed-disks.md) para ser capaz de alternar entre os tipos de disco.

 

## <a name="prerequisites"></a>Pré-requisitos

* Como a conversão requer uma reinicialização da máquina virtual (VM), você deve agendar a migração do armazenamento de disco durante uma janela de manutenção pré-existente.
* Se o disco não for gerenciado, primeiro [converta-o em um disco gerenciado para](convert-unmanaged-to-managed-disks.md) que você possa alternar entre as opções de armazenamento.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Alterne todos os discos gerenciados de uma VM entre Premium e Standard

Este exemplo mostra como converter todos os discos de uma VM do armazenamento Standard para Premium ou do armazenamento Premium para o Standard. Para usar discos gerenciados Premium, sua VM deve usar um [tamanho de VM](sizes.md) que suporte armazenamento Premium. Este exemplo também muda para um tamanho que suporte armazenamento premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Alternar discos gerenciados individuais entre Padrão e Premium

Para sua carga de trabalho de dev/teste, você pode querer uma mistura de discos Standard e Premium para reduzir seus custos. Você pode optar por atualizar apenas os discos que precisam de melhor desempenho. Este exemplo mostra como converter um único disco VM do armazenamento Standard para premium ou do armazenamento Premium para o Standard. Para usar discos gerenciados Premium, sua VM deve usar um [tamanho de VM](sizes.md) que suporte armazenamento Premium. Este exemplo também mostra como mudar para um tamanho que suporta armazenamento Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Converta discos gerenciados de Padrão para Premium no portal Azure

Siga estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione a VM na lista de **máquinas virtuais** no portal.
3. Se a VM não for parada, **selecione Parar** no painel visão **geral** superior da VM e aguarde que a VM pare.
3. No painel da VM, selecione **Discos** no menu.
4. Selecione o disco que deseja converter.
5. Selecione **Configuração** no menu.
6. Alterar o **tipo de conta** de **HDD padrão** para **SSD Premium**.
7. Clique **em Salvar**e feche o painel de disco.

A conversão do tipo de disco é instantânea. Você pode iniciar seu VM após a conversão.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Alternar discos gerenciados entre HDD padrão e SSD padrão 

Este exemplo mostra como converter um único disco VM de HDD padrão para SSD padrão ou de SSD padrão para HDD padrão:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando um [snapshot](snapshot-copy-managed-disk.md).
