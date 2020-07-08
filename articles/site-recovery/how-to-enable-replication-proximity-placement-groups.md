---
title: Replicar VMs do Azure em execução em grupos de posicionamento por proximidade
description: Saiba como replicar VMs do Azure em execução em grupos de posicionamento por proximidade usando o Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 9fabf6cf4c8a3afc2d119fca2c8cdc2526ddbebb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84415858"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Replicar máquinas virtuais do Azure em execução em grupos de posicionamento por proximidade para outra região

Este artigo descreve como replicar, executar failover e failback de máquinas virtuais em execução em um grupo de posicionamento por proximidade para uma região secundária.

Os [grupos de posicionamento por proximidade](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) são um recurso de agrupamento lógico de máquina virtual do Azure que você pode usar para diminuir a latência de rede entre VMs associada aos seus aplicativos. Quando as VMs são implantadas no mesmo grupo de posicionamento por proximidade, ficam fisicamente localizadas o mais próximo possível umas das outras. Os grupos de posicionamento por proximidade são particularmente úteis para atender aos requisitos de cargas de trabalho sensíveis à latência.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Recuperação de desastre para grupos de posicionamento por proximidade

Em um cenário típico, você pode ter suas máquinas virtuais em execução em um grupo de posicionamento por proximidade para evitar a latência de rede entre as várias camadas do aplicativo. Embora isso possa fornecer ao aplicativo latência de rede ideal, é conveniente que você proteja esses aplicativos usando o Site Recovery contra falhas em qualquer nível de região. O Site Recovery replica os dados de uma região para outra região do Azure e traz as máquinas na região de recuperação de desastre em um evento de failover.

## <a name="considerations"></a>Considerações

- O melhor esforço será fazer failover/failback das máquinas virtuais em um grupo de posicionamento por proximidade. No entanto, se a VM não puder ser colocada dentro do posicionamento por proximidade durante o failover/failback, o failover/failback ainda ocorrerá, e as máquinas virtuais serão criadas fora de um grupo de posicionamento por proximidade.
-  Se um conjunto de disponibilidade for fixado em um grupo de posicionamento por proximidade, e durante o failover/failback as VMs no conjunto de disponibilidade tiverem uma restrição de alocação, as máquinas virtuais serão criadas fora do conjunto de disponibilidade e do grupo de posicionamento por proximidade.
-  Não há suporte para o Site Recovery para grupos de posicionamento por proximidade em discos não gerenciados.

> [!Note]
> Azure Site Recovery não dá suporte ao failback de discos gerenciados para cenários do Hyper-V para o Azure. Portanto, não há suporte para o failback do grupo de posicionamento de proximidade no Azure para o Hyper-V.

## <a name="prerequisites"></a>Pré-requisitos

1. Garanta que você tem o módulo Az do Azure PowerShell. Se precisar instalar ou atualizar o Azure PowerShell, siga este [Guia para instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Configurar o Site Recovery para Máquinas Virtuais no grupo de posicionamento por proximidade

### <a name="azure-to-azure"></a>Azure para o Azure

1. [Entre](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#sign-in-to-your-microsoft-azure-subscription) na sua conta e selecione sua assinatura.
2. Obtenha os detalhes da máquina virtual que você planeja replicar conforme mencionado [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#get-details-of-the-virtual-machine-to-be-replicated).
3. [Crie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-recovery-services-vault) um cofre dos Serviços de Recuperação e [defina](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#set-the-vault-context) o contexto do cofre.
4. Prepare o cofre para iniciar a replicação de máquina virtual. Isso envolve a criação de um [objeto de Service Fabric](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) para as regiões primária e de recuperação.
5. [Crie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-protection-container-in-the-primary-fabric) um contêiner de proteção de Site Recovery, para as malhas primária e de recuperação.
6. [Crie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-replication-policy) uma política de replicação.
7. Crie um mapeamento de contêiner de proteção entre o contêiner de proteção principal e o de recuperação usando [estas](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) etapas e um mapeamento de contêiner de proteção para failback, conforme mencionado [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. Crie uma conta de armazenamento de cache seguindo [estas](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-cache-storage-account-and-target-storage-account) etapas.
9. Crie os mapeamentos de rede necessários, conforme mencionado [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-network-mappings).
10. Para replicar a máquina virtual do Azure com discos gerenciados, use o cmdlet do PowerShell abaixo – 

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $recPpg.Id
```
Quando a operação de replicação inicial for bem-sucedida, os dados da máquina virtual serão replicados para a região de recuperação.

O processo de replicação inicia-se propagando inicialmente uma cópia dos discos de replicação da máquina virtual na região de recuperação. Essa fase é chamada de fase de replicação inicial.

Depois que a replicação inicial for concluída, a replicação passará para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida, e uma operação de teste de failover pode ser executada nela. O estado de replicação do item replicado que representa a máquina virtual vai para o estado Protegido após a conclusão da replicação inicial.

Monitore o estado e a integridade da replicação para a máquina virtual obtendo detalhes do item de replicação protegido correspondente a ele. 

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Para fazer um teste de failover, validar e limpar o teste, siga [estas](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#do-a-test-failover-validate-and-cleanup-test-failover) etapas.
12. Para fazer failover, siga as etapas conforme mencionado [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#fail-over-to-azure).
13. Para proteger novamente e fazer failback para a região de origem, use o cmdlet do PowerShell abaixo –

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. Para desabilitar a replicação, siga as etapas [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#disable-replication).

### <a name="vmware-to-azure"></a>VMware no Azure

1. Não deixe de [preparar seus servidores VMware locais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises) para a recuperação de desastres para o Azure.
2. Entre em sua conta e defina a assinatura conforme especificado [aqui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#log-into-azure).
3. [Configure](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-up-a-recovery-services-vault) um cofre dos Serviços de Recuperação e [defina o contexto do cofre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-the-vault-context).
4. [Valide](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#validate-vault-registration) o registro do cofre.
5. [Crie](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-a-replication-policy) uma política de replicação.
6. [Adicione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#add-a-vcenter-server-and-discover-vms) um vCenter Server, descubra máquinas virtuais e [crie](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-storage-accounts-for-replication) contas de armazenamento para replicação.
7. Para replicar Máquinas Virtuais da VMware, confira os detalhes aqui e siga o cmdlet do PowerShell abaixo –

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $recPpg.Id
```
8. Você pode verificar o estado de replicação e a integridade da replicação da máquina virtual com o cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. Para definir as configurações de failover, siga as etapas [aqui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#configure-failover-settings).
10. [Execute](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#run-a-test-failover) um teste de failover. 
11. Failover para o Azure usando [estas](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#fail-over-to-azure) etapas.

### <a name="hyper-v-to-azure"></a>Hyper-V para Azure

1. Não deixe de [preparar seus servidores Hyper-V locais](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial) para a recuperação de desastres para o Azure.
2. [Entre](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-1-sign-in-to-your-azure-account) no Azure.
3. [Configure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-2-set-up-the-vault) seu cofre e [defina](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-3-set-the-recovery-services-vault-context) o contexto do cofre dos Serviços de Recuperação.
4. [Crie](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-4-create-a-hyper-v-site) um site Hyper-V.
5. [Instale](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-5-install-the-provider-and-agent) o provedor e o agente.
6. [Crie](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-6-create-a-replication-policy) uma política de replicação.
7. Habilite a replicação usando as etapas a seguir – 
    
    a. Recupere o item protegido que corresponde à VM que você deseja proteger, da seguinte maneira:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Proteja a VM. Se a VM que você estiver protegendo tiver mais de um disco anexado, será necessário especificar o disco do sistema operacional usando o parâmetro OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $recPpg.Id
    ```
    c. Aguarde até que as máquinas virtuais atinjam um estado protegido após a replicação inicial. Isso pode demorar um pouco, dependendo de fatores como a quantidade de dados a serem replicados e a largura de banda upstream disponível no Azure. Quando um estado protegido estiver implantado, o State e StateDescription do trabalho são atualizados da seguinte maneira: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Atualize as propriedades de recuperação (como o tamanho da função da VM) e a rede do Azure à qual o NIC da VM será anexado após o failover.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Execute um teste [de failover](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-8-run-a-test-failover).


## <a name="next-steps"></a>Próximas etapas

Para executar a nova proteção e failback para a VMware no Azure, siga as etapas descritas [aqui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-prepare-failback).

Para executar o failover do Hyper-V no Azure, siga as etapas descritas [aqui](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover). Para executar o failback, siga as etapas descritas [aqui](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-failback).

Para obter mais informações, consulte [Failover no Site Recovery](site-recovery-failover.md).
