---
title: Exclua os discos Azure VM da replicação com o Azure Site Recovery e o Azure PowerShell
description: Saiba como excluir discos de máquinas virtuais do Azure durante a Recuperação do Site do Azure usando o Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973670"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Exclua discos da replicação do PowerShell das VMs do Azure

Este artigo descreve como excluir discos quando você replica VMs do Azure. Você pode excluir discos para otimizar a largura de banda de replicação consumida ou os recursos do lado de destino que esses discos usam. Atualmente, esse recurso está disponível apenas através do Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar:

- Certifique-se de entender a [arquitetura e os componentes da recuperação de desastres](azure-to-azure-architecture.md).
- Revise os [requisitos de suporte](azure-to-azure-support-matrix.md) para todos os componentes.
- Certifique-se de que você tem o módulo AzureRm PowerShell "Az". Para instalar ou atualizar o PowerShell, consulte [Instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Certifique-se de que você criou um cofre de serviços de recuperação e máquinas virtuais protegidas pelo menos uma vez. Se você não tiver feito essas coisas, siga o processo de [recuperação de desastres configurado para máquinas virtuais Azure usando o Azure PowerShell](azure-to-azure-powershell.md).
- Se você está procurando informações sobre a adição de discos a uma VM do Azure habilitada para replicação, [revise este artigo](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Por que excluir discos da replicação
Você pode precisar excluir discos da replicação porque:

- Sua máquina virtual atingiu [os limites de recuperação do Site Do Azure para replicar as taxas de alteração de dados](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Os dados que estão no disco excluído não são importantes ou não precisam ser replicados.

- Você deseja salvar recursos de armazenamento e rede não replicando os dados.

## <a name="how-to-exclude-disks-from-replication"></a>Como excluir discos da replicação

Em nosso exemplo, replicamos uma máquina virtual que tem um SO e três discos de dados que está na região leste dos EUA para a região oeste dos EUA 2. O nome da máquina virtual é *AzureDemoVM*. Excluímos o disco 1 e mantemos os discos 2 e 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Obtenha detalhes das máquinas virtuais para replicar

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Obtenha detalhes sobre os discos da máquina virtual. Essas informações serão usadas mais tarde quando você iniciar a replicação da VM.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replicar uma máquina virtual Do Zure

Para o exemplo a seguir, assumimos que você já tem uma conta de armazenamento de cache, política de replicação e mapeamentos. Se você não tiver essas coisas, siga o processo de [recuperação de desastres configurado para máquinas virtuais Azure usando o Azure PowerShell](azure-to-azure-powershell.md).

Replique uma máquina virtual Azure com *discos gerenciados*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Quando a operação de inicial-replicação é bem sucedida, os dados da VM são replicados para a região de recuperação.

Você pode ir ao portal Azure e ver as VMs replicadas em "itens replicados".

O processo de replicação começa sedendo uma cópia dos discos replicantes da máquina virtual na região de recuperação. Esta fase é chamada de fase de replicação inicial.

Após o término da replicação inicial, a replicação passa para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida. Selecione a máquina virtual protegida para ver se algum disco está excluído.

## <a name="next-steps"></a>Próximas etapas

Aprenda a [executar um failover de teste](site-recovery-test-failover-to-azure.md).
