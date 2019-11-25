---
title: Azure Disk Encryption com VMs de IaaS Linux do Aplicativo Azure AD (versão anterior)
description: Este artigo fornece instruções sobre como habilitar as VMs da IaaS do Microsoft Azure Disk Encryption para Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d41f2138a453e4a34354c10bbebad41724a18d1d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457473"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Enable Azure Disk Encryption with Azure AD on Linux VMs (previous release)

The new release of Azure Disk Encryption eliminates the requirement for providing an Azure Active Directory (Azure AD) application parameter to enable VM disk encryption. Com a nova versão, não é mais necessário fornecer credenciais do Azure AD durante a etapa para habilitar criptografia. All new VMs must be encrypted without the Azure AD application parameters by using the new release. For instructions on how to enable VM disk encryption by using the new release, see [Azure Disk Encryption for Linux VMS](disk-encryption-linux.md). As VMs que já foram criptografadas com os parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.

You can enable many disk-encryption scenarios, and the steps might vary according to the scenario. The following sections cover the scenarios in greater detail for Linux infrastructure as a service (IaaS) VMs. You can only apply disk encryption to virtual machines of [supported VM sizes and operating systems](disk-encryption-overview.md#supported-vms-and-operating-systems). You must also meet the following prerequisites:

- [Additional requirements for VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Networking and Group Policy](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Encryption key storage requirements](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Take a [snapshot](snapshot-copy-managed-disk.md), make a backup, or both before you encrypt the disks. Os backups garantem que uma opção de recuperação seja possível, no caso de uma falha inesperada durante a criptografia. VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. After a backup is made, you can use the Set-AzVMDiskEncryptionExtension cmdlet to encrypt managed disks by specifying the -skipVmBackup parameter. For more information about how to back up and restore encrypted VMs, see [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - If you previously used [Azure Disk Encryption with the Azure AD app](disk-encryption-overview-aad.md) to encrypt this VM, you must continue to use this option to encrypt your VM. You can't use [Azure Disk Encryption](disk-encryption-overview.md) on this encrypted VM because this isn't a supported scenario, which means switching away from the Azure AD application for this encrypted VM isn't supported yet.
 > - To make sure the encryption secrets don't cross regional boundaries, Azure Disk Encryption needs the key vault and the VMs to be co-located in the same region. Create and use a key vault that's in the same region as the VM to be encrypted.
 > - When you encrypt Linux OS volumes, the process can take a few hours. It's normal for Linux OS volumes to take longer than data volumes to encrypt.
> - When you encrypt Linux OS volumes, the VM should be considered unavailable. We strongly recommend that you avoid SSH logins while the encryption is in progress to avoid blocking any open files that need to be accessed during the encryption process. To check progress, use the [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) or [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) commands. You can expect this process to take a few hours for a 30-GB OS volume, plus additional time for encrypting data volumes. Data volume encryption time is proportional to the size and quantity of the data volumes unless the **encrypt format all** option is used. 
 > - Desabilitar criptografia nas VMs do Linux tem suporte apenas para volumes de dados. It's not supported on data or OS volumes if the OS volume has been encrypted. 

 

## <a name="bkmk_RunningLinux"> </a> Habilitar criptografia em uma VM da IaaS do Linux existente ou em execução

In this scenario, you can enable encryption by using the Azure Resource Manager template, PowerShell cmdlets, or Azure CLI commands. 

>[!IMPORTANT]
 >It's mandatory to take a snapshot or back up a managed disk-based VM instance outside of and prior to enabling Azure Disk Encryption. You can take a snapshot of the managed disk from the Azure portal, or you can use [Azure Backup](../../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. After a backup is made, use the Set-AzVMDiskEncryptionExtension cmdlet to encrypt managed disks by specifying the -skipVmBackup parameter. The Set-AzVMDiskEncryptionExtension command fails against managed disk-based VMs until a backup is made and this parameter is specified. 
>
>Encrypting or disabling encryption might cause the VM to reboot. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Enable encryption on an existing or running Linux VM by using the Azure CLI 
You can enable disk encryption on your encrypted VHD by installing and using the [Azure CLI 2.0](/cli/azure) command-line tool. Você pode usá-lo em seu navegador com o [Azure Cloud Shell](../../cloud-shell/overview.md), ou você pode instalá-lo em seu computador local e usá-lo em qualquer sessão do PowerShell. Para habilitar a criptografia em VMs da IaaS do Linux existentes ou em execução no Azure, use os comandos a seguir da CLI:

Use o comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure.

-  **Encrypt a running VM by using a client secret:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Encrypt a running VM by using KEK to wrap the client secret:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > The syntax for the value of the disk-encryption-keyvault parameter is the full identifier string: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> The syntax for the value of the key-encryption-key parameter is the full URI to the KEK as in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Verify that the disks are encrypted:** To check on the encryption status of an IaaS VM, use the [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) command. 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desabilitar criptografia:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Enable encryption on an existing or running Linux VM by using PowerShell
Use the [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet to enable encryption on a running IaaS virtual machine in Azure. Take a [snapshot](snapshot-copy-managed-disk.md) or make a backup of the VM with [Azure Backup](../../backup/backup-azure-vms-encryption.md) before the disks are encrypted. The -skipVmBackup parameter is already specified in the PowerShell scripts to encrypt a running Linux VM.

- **Encrypt a running VM by using a client secret:** The following script initializes your variables and runs the Set-AzVMDiskEncryptionExtension cmdlet. The resource group, VM, key vault, Azure AD app, and client secret should have already been created as prerequisites. Replace MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID, and My-AAD-client-secret with your values. Modify the -VolumeType parameter to specify which disks you're encrypting.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Encrypt a running VM by using KEK to wrap the client secret:** Azure Disk Encryption lets you specify an existing key in your key vault to wrap disk encryption secrets that were generated while enabling encryption. When a key encryption key is specified, Azure Disk Encryption uses that key to wrap the encryption secrets before writing to the key vault. Modify the -VolumeType parameter to specify which disks you're encrypting. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > The syntax for the value of the disk-encryption-keyvault parameter is the full identifier string: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  The syntax for the value of the key-encryption-key parameter is the full URI to the KEK as in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Verify that the disks are encrypted:** To check on the encryption status of an IaaS VM, use the [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Desabilitar criptografia de disco:** para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Habilitar criptografia em uma VM da IaaS do Linux existente ou em execução com um modelo

Você pode habilitar a criptografia de disco em uma VM Linux IaaS existente ou em execução no Azure usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Select **Deploy to Azure** on the Azure quickstart template.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Select **Create** to enable encryption on the existing or running IaaS VM.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos existente ou VMs em execução que usam uma ID de cliente do Azure AD:

| . | Descrição |
| --- | --- |
| AADClientID | ID do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo AD do Azure que tem permissões para gravar segredos para o cofre de chaves. |
| keyVaultName | Nome do cofre de chaves em que a chave deve ser carregada. É possível obtê-lo, usando o comando da CLI do Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL da chave de criptografia de chave usada para criptografar a chave gerada. This parameter is optional if you select **nokek** in the **UseExistingKek** drop-down list. If you select **kek** in the **UseExistingKek** drop-down list, you must enter the _keyEncryptionKeyURL_ value. |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Valid supported values are _OS_ or _All_. (See supported Linux distributions and their versions for OS and data disks in the prerequisites section earlier.) |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão sempre que uma operação de criptografia de disco for executada na mesma VM. |
| vmName | Nome da VM em que a operação de criptografia deve ser executada. |
| senha | Digite uma frase secreta forte como chave de criptografia de dados. |



## <a name="bkmk_EFA"> </a>Use the EncryptFormatAll feature for data disks on Linux IaaS VMs
The EncryptFormatAll parameter reduces the time for Linux data disks to be encrypted. Partitions that meet certain criteria are formatted (with their current file system). Then they're remounted back to where they were before command execution. If you want to exclude a data disk that meets the criteria, you can unmount it before you run the command.

 After you run this command, any drives that were mounted previously are formatted. Then the encryption layer starts on top of the now empty drive. When this option is selected, the ephemeral resource disk attached to the VM is also encrypted. If the ephemeral drive is reset, it's reformatted and re-encrypted for the VM by the Azure Disk Encryption solution at the next opportunity.

>[!WARNING]
> EncryptFormatAll shouldn't be used when there's needed data on a VM's data volumes. You can exclude disks from encryption by unmounting them. Try out the EncryptFormatAll parameter on a test VM first to understand the feature parameter and its implication before you try it on the production VM. The EncryptFormatAll option formats the data disk, so all the data on it will be lost. Before you proceed, verify that any disks you want to exclude are properly unmounted. </br></br>
 >If you set this parameter while you update encryption settings, it might lead to a reboot before the actual encryption. In this case, you also want to remove the disk you don't want formatted from the fstab file. Similarly, you should add the partition you want encrypt-formatted to the fstab file before you initiate the encryption operation. 

### <a name="bkmk_EFACriteria"> </a> Critérios do EncryptFormatAll
The parameter goes through all partitions and encrypts them as long as they meet *all* of the following criteria: 
- Não é uma partição de inicialização/SO/raiz
- Ainda não está criptografado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Criptografe os discos que compõem o volume RAID ou LVM em vez do volume RAID ou LVM.

### <a name="bkmk_EFATemplate"> </a> Usar o parâmetro EncryptFormatAll com um modelo
To use the EncryptFormatAll option, use any preexisting Azure Resource Manager template that encrypts a Linux VM and change the **EncryptionOperation** field for the AzureDiskEncryption resource.

1. Como exemplo, use o [modelo do Resource Manager para criptografar uma VM da IaaS do Linux em execução](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Select **Deploy to Azure** on the Azure quickstart template.
3. Change the **EncryptionOperation** field from **EnableEncryption** to **EnableEncryptionFormatAl**.
4. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, outros parâmetros, termos legais e contrato. Select **Create** to enable encryption on the existing or running IaaS VM.


### <a name="bkmk_EFAPSH"> </a> Usar o parâmetro EncryptFormatAll com um cmdlet do PowerShell
Use the [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet with the EncryptFormatAll parameter.

**Encrypt a running VM by using a client secret and EncryptFormatAll:** As an example, the following script initializes your variables and runs the Set-AzVMDiskEncryptionExtension cmdlet with the EncryptFormatAll parameter. The resource group, VM, key vault, Azure AD app, and client secret should have already been created as prerequisites. Replace MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID, and My-AAD-client-secret with your values.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Usar o parâmetro EncryptFormatAll com LVM (Gerenciador de Volume Lógico) 
É recomendável uma configuração LVM-on-crypt. For all the following examples, replace the device-path and mountpoints with whatever suits your use case. Essa configuração pode ser feita da seguinte maneira:

- Adicione os discos de dados que irão compor a VM.
- Formatar, montar e adicionar esses discos ao arquivo fstab.

    1. Formate o disco adicionado recentemente. Usamos symlinks gerados pelo Azure aqui. O uso de symlinks evita problemas relacionados à alteração de nomes de dispositivos. For more information, see [Troubleshoot device names problems](troubleshoot-device-names-problems.md).
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Monte os discos.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Adicione ao fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Run the Set-AzVMDiskEncryptionExtension PowerShell cmdlet with -EncryptFormatAll to encrypt these disks.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Configure a LVM sobre esses novos discos. Observe que as unidades criptografadas serão desbloqueadas após a VM concluir a inicialização. Portanto, a montagem de LVM também deverá ser subsequentemente atrasada.




## <a name="bkmk_VHDpre"> </a> Novas VMs da IaaS criadas a partir de chaves de criptografia e VHD criptografado pelo cliente
Nesse cenário, você pode habilitar a criptografia usando o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam detalhadamente o modelo do Gerenciador de Recursos e comandos da CLI. 

Use as instruções no apêndice para preparar imagens previamente criptografadas que podem ser usadas no Azure. Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.

* [Preparar um VHD Linux previamente criptografado](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >It's mandatory to take a snapshot or back up a managed disk-based VM instance outside of and prior to enabling Azure Disk Encryption. You can take a snapshot of the managed disk from the portal, or you can use [Azure Backup](../../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. After a backup is made, use the Set-AzVMDiskEncryptionExtension cmdlet to encrypt managed disks by specifying the -skipVmBackup parameter. The Set-AzVMDiskEncryptionExtension command fails against managed disk-based VMs until a backup is made and this parameter is specified. 
>
>Encrypting or disabling encryption might cause the VM to reboot.



### <a name="bkmk_VHDprePSH"> </a> Usar o Azure PowerShell para criptografar VMs da IaaS com VHDs previamente criptografados 
You can enable disk encryption on your encrypted VHD by using the PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). The following example gives you some common parameters. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Habilitar criptografia em um disco de dados adicionado recentemente
You can add a new data disk by using [az vm disk attach](add-disk.md) or [through the Azure portal](attach-disk-portal.md). Antes de poder criptografar, primeiro será necessário montar o disco de dados anexado recentemente. You must request encryption of the data drive because the drive will be unusable while encryption is in progress. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Enable encryption on a newly added disk with the Azure CLI
 If the VM was previously encrypted with "All," then the --volume-type parameter should remain All. Todos inclui o sistema operacional e os discos de dados. If the VM was previously encrypted with a volume type of "OS," then the --volume-type parameter should be changed to All so that both the OS and the new data disk will be included. If the VM was encrypted with only the volume type of "Data," then it can remain Data as demonstrated here. Adding and attaching a new data disk to a VM isn't sufficient preparation for encryption. The newly attached disk must also be formatted and properly mounted within the VM before you enable encryption. On Linux, the disk must be mounted in /etc/fstab with a [persistent block device name](troubleshoot-device-names-problems.md). 

In contrast to Powershell syntax, the CLI doesn't require you to provide a unique sequence version when you enable encryption. A CLI gera e usa automaticamente o próprio valor de versão de sequência exclusivo.

-  **Encrypt a running VM by using a client secret:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Encrypt a running VM by using KEK to wrap the client secret:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Habilitar criptografia em um disco adicionado recentemente com Azure PowerShell
 When you use Powershell to encrypt a new disk for Linux, a new sequence version needs to be specified. A versão da sequência deverá ser exclusiva. The following script generates a GUID for the sequence version. 
 

-  **Encrypt a running VM by using a client secret:** The following script initializes your variables and runs the Set-AzVMDiskEncryptionExtension cmdlet. The resource group, VM, key vault, Azure AD app, and client secret should have already been created as prerequisites. Replace MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID, and My-AAD-client-secret with your values. O parâmetro -VolumeType é configurado para discos de dados e não para o disco de SO. If the VM was previously encrypted with a volume type of "OS" or "All," then the -VolumeType parameter should be changed to All so that both the OS and the new data disk will be included.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Encrypt a running VM by using KEK to wrap the client secret:** Azure Disk Encryption lets you specify an existing key in your key vault to wrap disk encryption secrets that were generated while enabling encryption. When a key encryption key is specified, Azure Disk Encryption uses that key to wrap the encryption secrets before writing to the key vault. O parâmetro -VolumeType é configurado para discos de dados e não para o disco de SO. If the VM was previously encrypted with a volume type of "OS" or "All," then the -VolumeType parameter should be changed to All so that both the OS and the new data disk will be included.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> The syntax for the value of the disk-encryption-keyvault parameter is the full identifier string: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
The syntax for the value of the key-encryption-key parameter is the full URI to the KEK as in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Desabilitar criptografia para VMs do Linux
You can disable encryption by using Azure PowerShell, the Azure CLI, or a Resource Manager template. 

>[!IMPORTANT]
>Desabilitar criptografia com Azure Disk Encryption em VMs do Linux tem suporte apenas para volumes de dados. It's not supported on data or OS volumes if the OS volume has been encrypted. 

- **Disable disk encryption with Azure PowerShell:** To disable encryption, use the [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Desabilitar criptografia com a CLI do Azure:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Disable encryption with a Resource Manager template:** To disable encryption, use the [Disable encryption on a running Linux VM](https://aka.ms/decrypt-linuxvm) template.
     1. Select **Deploy to Azure**.
     2. Selecione a assinatura, o grupo de recursos, o local, a VM, os termos legais e o contrato.
     3. Select **Purchase** to disable disk encryption on a running Windows VM. 


## <a name="next-steps"></a>Próximos passos

- [Azure Disk Encryption for Linux overview](disk-encryption-overview-aad.md)
- [Creating and configuring a key vault for Azure Disk Encryption with Azure AD (previous release)](disk-encryption-key-vault-aad.md)
