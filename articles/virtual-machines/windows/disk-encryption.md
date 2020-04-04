---
title: Criptografia do lado do servidor dos discos gerenciados do Azure - PowerShell
description: O Azure Storage protege seus dados criptografando-os em repouso antes de permê-los em clusters de armazenamento. Você pode confiar em chaves gerenciadas pela Microsoft para a criptografia de seus discos gerenciados ou usar chaves gerenciadas pelo cliente para gerenciar criptografia com suas próprias chaves.
author: roygara
ms.date: 04/02/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: d8e6f4b58cf17a7502e2c0d67e4fa67af7cdb3f5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632943"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Criptografia do lado do servidor dos discos gerenciados do Azure

Os discos gerenciados do Azure criptografam automaticamente seus dados por padrão ao persistirem na nuvem. A criptografia do lado do servidor protege seus dados e ajuda você a cumprir seus compromissos de segurança organizacional e conformidade. Os dados em discos gerenciados do Azure são criptografados de forma transparente usando [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das cifras de bloco mais fortes disponíveis, e é compatível com FIPS 140-2.

A criptografia não afeta o desempenho dos discos gerenciados. Não há custo adicional para a criptografia.

Para obter mais informações sobre os módulos criptográficos subjacentes aos discos gerenciados pelo Azure, consulte [API de criptografia: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Você pode confiar em chaves gerenciadas pela plataforma para a criptografia do seu disco gerenciado ou gerenciar a criptografia usando suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, você pode especificar uma *chave gerenciada pelo cliente* para usar para criptografar e descriptografar todos os dados em discos gerenciados. 

As seções a seguir descrevem cada uma das opções para gerenciamento de chaves com mais detalhes.

## <a name="platform-managed-keys"></a>Chaves gerenciadas por plataforma

Por padrão, os discos gerenciados usam chaves de criptografia gerenciadas pela plataforma. A partir de 10 de junho de 2017, todos os novos discos gerenciados, snapshots, imagens e novos dados gravados em discos gerenciados existentes são criptografados automaticamente com chaves gerenciadas pela plataforma.

## <a name="customer-managed-keys"></a>Chaves gerenciadas pelo cliente

Você pode optar por gerenciar a criptografia no nível de cada disco gerenciado, com suas próprias chaves. A criptografia do lado do servidor para discos gerenciados com chaves gerenciadas pelo cliente oferece uma experiência integrada com o Azure Key Vault. Você pode importar [suas chaves RSA](../../key-vault/key-vault-hsm-protected-keys.md) para o seu Cofre de Chaves ou gerar novas chaves RSA no Azure Key Vault. 

Os discos gerenciados do Azure lidam com a criptografia e a descriptografia de forma totalmente transparente usando [criptografia de envelopes](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Ele criptografa dados usando uma chave de criptografia de dados baseada em [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), que, por sua vez, é protegida usando suas chaves. O serviço de armazenamento gera chaves de criptografia de dados e as criptografa com chaves gerenciadas pelo cliente usando criptografia RSA. A criptografia do envelope permite que você gire (alterar) suas chaves periodicamente de acordo com suas políticas de conformidade sem afetar suas VMs. Quando você gira suas chaves, o serviço de armazenamento recriptografa as chaves de criptografia de dados com as novas chaves gerenciadas pelo cliente. 

Você tem que conceder acesso a discos gerenciados em seu Cofre de Chaves para usar suas chaves para criptografar e descriptografar o DEK. Isso permite que você tenha controle total de seus dados e chaves. Você pode desativar suas chaves ou revogar o acesso a discos gerenciados a qualquer momento. Você também pode auditar o uso da chave de criptografia com o monitoramento do Azure Key Vault para garantir que apenas discos gerenciados ou outros serviços confiáveis do Azure estejam acessando suas chaves.

Para SSDs premium, SSDs padrão e HDDs padrão: Quando você desativar ou excluir sua chave, quaisquer VMs com discos usando essa chave serão automaticamente desligados. Depois disso, as VMs não serão utilizáveis a menos que a chave esteja ativada novamente ou você atribua uma nova chave.

Para discos ultra, quando você desativar ou excluir uma chave, quaisquer VMs com discos ultra usando a chave não serão automaticamente desligados. Depois de desalocar e reiniciar as VMs, os discos pararão de usar a chave e, em seguida, as VMs não voltarão a funcionar. Para colocar as VMs novamente on-line, você deve atribuir uma nova chave ou ativar a chave existente.

O diagrama a seguir mostra como os discos gerenciados usam o Azure Active Directory e o Azure Key Vault para fazer solicitações usando a chave gerenciada pelo cliente:

![Fluxo de trabalho gerenciado em disco e chaves gerenciadas pelo cliente. Um administrador cria um Azure Key Vault, cria um conjunto de criptografia de disco e configura o conjunto de criptografia de disco. O Conjunto está associado a uma VM, que permite que o disco faça uso do Azure AD para autenticar](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


A lista a seguir explica o diagrama com mais detalhes:

1. Um administrador do Azure Key Vault cria recursos de cofre chave.
1. O admin do cofre chave importa suas chaves RSA para key vault ou gera novas chaves RSA no Key Vault.
1. Esse administrador cria uma instância do recurso Disk Encryption Set, especificando um ID do Cofre de Chaves do Azure e uma URL chave. O Disk Encryption Set é um novo recurso introduzido para simplificar o gerenciamento de chaves para discos gerenciados. 
1. Quando um conjunto de criptografia de disco é criado, uma [identidade gerenciada atribuída ao sistema](../../active-directory/managed-identities-azure-resources/overview.md) é criada no Azure Active Directory (AD) e associada ao conjunto de criptografia de disco. 
1. O administrador do cofre chave do Azure concede então a permissão de identidade gerenciada para executar operações no cofre-chave.
1. Um usuário de VM cria discos associando-os ao conjunto de criptografia de disco. O usuário de VM também pode habilitar a criptografia do lado do servidor com chaves gerenciadas pelo cliente para os recursos existentes, associando-os ao conjunto de criptografia de disco. 
1. Os discos gerenciados usam a identidade gerenciada para enviar solicitações ao Cofre de Chaves do Azure.
1. Para ler ou escrever dados, os discos gerenciados enviam solicitações ao Azure Key Vault para criptografar (embrulhar) e descriptografar (desembrulhar) a chave de criptografia de dados para executar a criptografia e a descriptografia dos dados. 

Para revogar o acesso às chaves gerenciadas pelo cliente, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). A revogação do acesso bloqueia efetivamente o acesso a todos os dados da conta de armazenamento, já que a chave de criptografia é inacessível pelo Azure Storage.

### <a name="supported-regions"></a>Regiões com suporte

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Restrições

Por enquanto, as chaves gerenciadas pelo cliente têm as seguintes restrições:

- Se esse recurso estiver habilitado para o disco, não será possível desativá-lo.
    Se você precisar contornar isso, você deve [copiar todos os dados](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) para um disco gerenciado totalmente diferente que não esteja usando chaves gerenciadas pelo cliente.
- Apenas [as teclas RSA "macias" e "duras"](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) do tamanho 2080 são suportadas, sem outras chaves ou tamanhos.
- Os discos criados a partir de imagens personalizadas que são criptografadas usando criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados usando as mesmas chaves gerenciadas pelo cliente e devem estar na mesma assinatura.
- Os snapshots criados a partir de discos criptografados com criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados com as mesmas chaves gerenciadas pelo cliente.
- Imagens personalizadas criptografadas usando criptografia do lado do servidor e chaves gerenciadas pelo cliente não podem ser usadas na galeria de imagens compartilhadas.
- Todos os recursos relacionados às suas chaves gerenciadas pelo cliente (Azure Key Vaults, conjuntos de criptografia de disco, VMs, discos e snapshots) devem estar na mesma assinatura e região.
- Discos, instantâneos e imagens criptografadas com chaves gerenciadas pelo cliente não podem ser mover para outra assinatura.
- Se você usar o portal Azure para criar seu conjunto de criptografia de disco, você não poderá usar instantâneos por enquanto.
- Discos gerenciados criptografados usando chaves gerenciadas pelo cliente também não podem ser criptografados com criptografia de disco do Azure.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configurando seu cofre de chaves do Azure e diskEncryptionSet

1. Certifique-se de que você instalou a versão mais recente [do Azure PowerShell](/powershell/azure/install-az-ps)e você está conectado a uma conta do Azure com o Connect-AzAccount

1. Crie uma instância do Azure Key Vault e da chave de criptografia.

    Ao criar a instância do Cofre de Chaves, você deve ativar a proteção de exclusão suave e purga. A exclusão suave garante que o Cofre de Chaves mantenha uma chave excluída para um determinado período de retenção (padrão de 90 dias). A proteção contra expurgo garante que uma chave excluída não possa ser excluída permanentemente até que o período de retenção seja apagado. Essas configurações protegem você de perder dados devido à exclusão acidental. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados.

    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    Crie uma instância de um DiskEncryptionSet. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Conceda ao recurso DiskEncryptionSet acesso ao cofre principal.

        > [!NOTE]
        > Pode levar alguns minutos para o Azure criar a identidade do diskEncryptionSet em seu Diretório Ativo do Azure. Se você tiver um erro como "Não é possível encontrar o objeto Active Directory" ao executar o seguinte comando, espere alguns minutos e tente novamente.
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie uma VM usando uma imagem do Marketplace, criptografando o Sistema Operacional e os discos de dados com chaves gerenciadas pelo cliente

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Crie um disco vazio criptografado usando criptografia do lado do servidor com chaves gerenciadas pelo cliente e anexe-a a uma VM

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-unattached-managed-disks"></a>Criptografe discos gerenciados não conectados existentes 

Os discos existentes não devem ser anexados a uma VM em execução para que você os criptografe usando o seguinte script:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie um conjunto de escala de máquina virtual usando uma imagem do Marketplace, criptografando o Sistema Operacional e os discos de dados com chaves gerenciadas pelo cliente

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Alterar a chave de um DiskEncryptionSet para girar a chave para todos os recursos que fazem referência ao DiskEncryptionSet

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Encontre o status da criptografia do lado do servidor de um disco

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para os recursos do Azure, um recurso do Azure Active Directory (Azure AD). Quando você configura as chaves gerenciadas pelo cliente, uma identidade gerenciada é automaticamente atribuída aos seus recursos sob as cobertas. Se você posteriormente mover a assinatura, o grupo de recursos ou o disco gerenciado de um diretório Azure AD para outro, a identidade gerenciada associada aos discos gerenciados não será transferida para o novo inquilino, de modo que as chaves gerenciadas pelo cliente podem não funcionar mais. Para obter mais informações, consulte [Transferir uma assinatura entre os diretórios AD do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para os recursos do Azure, um recurso do Azure Active Directory (Azure AD). Quando você configura as chaves gerenciadas pelo cliente, uma identidade gerenciada é automaticamente atribuída aos seus recursos sob as cobertas. Se você posteriormente mover a assinatura, o grupo de recursos ou o disco gerenciado de um diretório Azure AD para outro, a identidade gerenciada associada aos discos gerenciados não será transferida para o novo inquilino, de modo que as chaves gerenciadas pelo cliente podem não funcionar mais. Para obter mais informações, consulte [Transferir uma assinatura entre os diretórios AD do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Criptografia do lado do servidor versus criptografia de disco Azure

[O Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) aproveita o recurso [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para criptografar discos gerenciados com chaves gerenciadas pelo cliente dentro da VM convidada.  A criptografia do lado do servidor com chaves gerenciadas pelo cliente melhora no ADE, permitindo que você use quaisquer tipos e imagens do Sistema operacional para suas VMs criptografando dados no serviço de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- [Explorar os modelos do Azure Resource Manager para criar discos criptografados com chaves gerenciadas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Cofre da Chave do Azure?](../../key-vault/key-vault-overview.md)
- [Replicar máquinas com discos habilitados para chaves gerenciadas pelo cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de desastre de VMs VMware para o Azure usando o PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurar a recuperação de desastres para o Azure para máquinas virtuais do Hyper-V usando o PowerShell e o Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
