---
title: Converta o armazenamento de discos gerenciados entre ssd padrão e premium
description: Como converter o armazenamento de discos gerenciados do Azure de padrão para premium ou premium para padrão usando o Cli do Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431499"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Converta o armazenamento de discos gerenciados do Azure de Padrão para Premium ou Premium para Padrão

Existem quatro tipos de discos gerenciados pelo Azure: SSDs ultra (visualização) azure, SSD premium, SSD padrão e HDD padrão. Você pode alternar entre os três tipos de disco GA (SSD premium, SSD padrão e HDD padrão) com base nas suas necessidades de desempenho. Você ainda não é capaz de mudar de ou para um Ultra SSD, você deve implantar um novo.

Essa funcionalidade não é suportada para discos não gerenciados. Mas você pode facilmente [converter um disco não gerenciado em um disco gerenciado](convert-unmanaged-to-managed-disks.md) para ser capaz de alternar entre os tipos de disco.

Este artigo mostra como converter discos gerenciados de Padrão para Premium ou Premium para Padrão usando o Cli do Azure. Para instalar ou atualizar a ferramenta, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

* A conversão de disco requer uma reinicialização da máquina virtual (VM), então agende a migração do armazenamento em disco durante uma janela de manutenção pré-existente.
* Para discos não gerenciados, primeiro [converta em discos gerenciados para](convert-unmanaged-to-managed-disks.md) que você possa alternar entre as opções de armazenamento.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Alterne todos os discos gerenciados de uma VM entre Premium e Standard

Este exemplo mostra como converter todos os discos de uma VM do armazenamento Standard para Premium ou do armazenamento Premium para o Standard. Para usar discos gerenciados Premium, sua VM deve usar um [tamanho de VM](sizes.md) que suporte armazenamento Premium. Este exemplo também muda para um tamanho que suporta armazenamento Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Alternar discos gerenciados individuais entre Padrão e Premium

Para sua carga de trabalho de dev/teste, você pode querer ter uma mistura de discos Standard e Premium para reduzir seus custos. Você pode optar por atualizar apenas os discos que precisam de melhor desempenho. Este exemplo mostra como converter um único disco VM do armazenamento Standard para premium ou do armazenamento Premium para o Standard. Para usar discos gerenciados Premium, sua VM deve usar um [tamanho de VM](sizes.md) que suporte armazenamento Premium. Este exemplo também muda para um tamanho que suporta armazenamento Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Alternar discos gerenciados entre HDD padrão e SSD padrão

Este exemplo mostra como converter um único disco VM de HDD padrão para SSD padrão ou de SSD padrão para HDD padrão.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Alternar discos gerenciados entre padrão e premium no portal Azure

Siga estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione a VM na lista de **máquinas virtuais**.
3. Se a VM não for parada, **selecione Parar** na parte superior do painel **visão geral** da VM e aguarde que a VM pare.
4. No painel da VM, selecione **Discos** no menu.
5. Selecione o disco que deseja converter.
6. Selecione **Configuração** no menu.
7. Mude o **tipo de conta** de **HDD padrão** para **SSD premium** ou de **SSD premium** para **HDD padrão**.
8. Selecione **Salvar**e feche o painel de disco.

A atualização do tipo de disco é instantânea. Você pode reiniciar sua VM após a conversão.

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).
