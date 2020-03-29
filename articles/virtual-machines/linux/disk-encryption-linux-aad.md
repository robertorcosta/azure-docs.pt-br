---
title: Azure Disk Encryption com VMs de IaaS Linux do Aplicativo Azure AD (versão anterior)
description: Este artigo fornece instruções sobre como habilitar as VMs da IaaS do Microsoft Azure Disk Encryption para Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970620"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Habilite a criptografia de disco do Azure com o Azure AD em VMs Linux (versão anterior)

A nova versão do Azure Disk Encryption elimina a exigência de fornecer um parâmetro de aplicativo Azure Active Directory (Azure AD) para permitir a criptografia de disco VM. Com a nova versão, não é mais necessário fornecer credenciais do Azure AD durante a etapa para habilitar criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo Azure AD usando a nova versão. Para obter instruções sobre como ativar a criptografia de disco VM usando a nova versão, consulte [Azure Disk Encryption for Linux VMS](disk-encryption-linux.md). As VMs que já foram criptografadas com os parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.

Você pode habilitar muitos cenários de criptografia de disco, e as etapas podem variar de acordo com o cenário. As seções a seguir cobrem os cenários com mais detalhes para a infra-estrutura Linux como um serviço (IaaS) VMs. Você só pode aplicar criptografia de disco a máquinas virtuais de [tamanhos de VM suportados e sistemas operacionais](disk-encryption-overview.md#supported-vms-and-operating-systems). Você também deve atender aos seguintes pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Política de networking e grupo](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Requisitos de armazenamento de chaves de criptografia](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Tire um [instantâneo,](snapshot-copy-managed-disk.md)faça um backup ou ambos antes de criptografar os discos. Os backups garantem que uma opção de recuperação seja possível, no caso de uma falha inesperada durante a criptografia. VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. Depois que um backup é feito, você pode usar o cmdlet Set-AzVMDiskEncryptionExtension para criptografar discos gerenciados especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Se você usou anteriormente [o Azure Disk Encryption com o aplicativo Azure AD](disk-encryption-overview-aad.md) para criptografar esta VM, você deve continuar a usar essa opção para criptografar sua VM. Você não pode usar [o Azure Disk Encryption](disk-encryption-overview.md) nesta VM criptografada porque este não é um cenário suportado, o que significa que mudar para longe do aplicativo Azure AD para este VM criptografado ainda não é suportado.
 > - Para garantir que os segredos de criptografia não ultrapassem as fronteiras regionais, o Azure Disk Encryption precisa que o cofre de chaves e as VMs sejam co-localizados na mesma região. Crie e use um cofre de chaves que esteja na mesma região que a VM para ser criptografado.
 > - Quando você criptografa os volumes do Sistema Operacional Linux, o processo pode levar algumas horas. É normal que os volumes do Sistema Operacional Linux demorem mais do que os volumes de dados para criptografar.
> - Quando você criptografa os volumes do Sistema Operacional Linux, o VM deve ser considerado indisponível. Recomendamos fortemente que você evite logins SSH enquanto a criptografia estiver em andamento para evitar o bloqueio de quaisquer arquivos abertos que precisem ser acessados durante o processo de criptografia. Para verificar o progresso, use os comandos [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) ou [vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) Você pode esperar que este processo leve algumas horas para um volume de sistema operacional de 30 GB, além de tempo adicional para criptografar volumes de dados. O tempo de criptografia do volume de dados é proporcional ao tamanho e quantidade dos volumes de dados, a menos que o **formato de criptografia de toda** a opção seja usado. 
 > - Desabilitar criptografia nas VMs do Linux tem suporte apenas para volumes de dados. Não é suportado em dados ou volumes do Sistema Operacional se o volume do Sistema Operacional tiver sido criptografado. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Habilitar criptografia em uma VM da IaaS do Linux existente ou em execução

Neste cenário, você pode habilitar a criptografia usando o modelo do Azure Resource Manager, cmdlets PowerShell ou comandos Azure CLI. 

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo ou fazer backup de uma instância de VM gerenciada baseada em disco fora e antes de habilitar a criptografia de disco Do Zure. Você pode tirar um instantâneo do disco gerenciado do portal Azure ou usar o [Azure Backup](../../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, use o cmdlet Set-AzVMDiskEncryptionExtension para criptografar discos gerenciados especificando o parâmetro -skipVmBackup. O comando Set-AzVMDiskEncryptionExtensioné o comando falha contra VMs gerenciadas baseadas em disco até que um backup seja feito e este parâmetro seja especificado. 
>
>Criptografar ou desativar a criptografia pode fazer com que a VM seja reiniciada. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Habilite a criptografia em um VM Linux existente ou executando usando o Azure CLI 
Você pode habilitar a criptografia de disco em seu VHD criptografado instalando e usando a ferramenta de linha de comando [Azure CLI 2.0.](/cli/azure) Você pode usá-lo em seu navegador com o [Azure Cloud Shell](../../cloud-shell/overview.md), ou você pode instalá-lo em seu computador local e usá-lo em qualquer sessão do PowerShell. Para habilitar a criptografia em VMs da IaaS do Linux existentes ou em execução no Azure, use os comandos a seguir da CLI:

Use o comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure.

-  **Criptografe uma VM em execução usando um segredo de cliente:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografe um VM em execução usando o KEK para envolver o segredo do cliente:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > A sintaxe para o valor do parâmetro disk-encryption-keyvault é a seqüência de identificadores completos: /assinaturas/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o comando [az vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desabilitar criptografia:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Habilite a criptografia em um VM Linux existente ou executando usando o PowerShell
Use o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual IaaS em execução no Azure. Tire uma [foto](snapshot-copy-managed-disk.md) ou faça um backup da VM com o [Backup do Azure](../../backup/backup-azure-vms-encryption.md) antes que os discos sejam criptografados. O parâmetro -skipVmBackup já está especificado nos scripts do PowerShell para criptografar um VM Linux em execução.

- **Criptografe uma VM em execução usando um segredo de cliente:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, VM, key vault, aplicativo Azure AD e o segredo do cliente já deveriam ter sido criados como pré-requisitos. Substitua myVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret por seus valores. Modifique o parâmetro -VolumeType para especificar quais discos você está criptografando.

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
- **Criptografe um VM em execução usando o KEK para envolver o segredo do cliente:** O Azure Disk Encryption permite especificar uma chave existente no cofre de chaves para envolver segredos de criptografia de disco que foram gerados ao ativar a criptografia. Quando uma chave de criptografia é especificada, o Azure Disk Encryption usa essa chave para envolver os segredos de criptografia antes de escrever no cofre de chaves. Modifique o parâmetro -VolumeType para especificar quais discos você está criptografando. 

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
  > A sintaxe para o valor do parâmetro disk-encryption-keyvault é a seqüência de identificadores completos: /assinaturas/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o [cmdlet Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Desabilitar criptografia de disco:** para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Habilitar criptografia em uma VM da IaaS do Linux existente ou em execução com um modelo

Você pode habilitar a criptografia de disco em uma VM Linux IaaS existente ou em execução no Azure usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Selecione **Implantar para o Azure** no modelo de partida rápida do Azure.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Selecione **Criar** para ativar a criptografia na VM IaaS existente ou em execução.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos existente ou VMs em execução que usam uma ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | ID do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo AD do Azure que tem permissões para gravar segredos para o cofre de chaves. |
| keyVaultName | Nome do cofre de chaves em que a chave deve ser carregada. É possível obtê-lo, usando o comando da CLI do Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL da chave de criptografia de chave usada para criptografar a chave gerada. Este parâmetro é opcional se você selecionar **nokek** na lista de parada **useExistingKek.** Se você selecionar **kek** na lista de isento **UseExistingKek,** você deve inserir o valor _keyEncryptionKeyURL._ |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Os valores suportados válidos _são sO_ ou _All_. (Consulte distribuições Linux suportadas e suas versões para sO e discos de dados na seção pré-requisitos anteriormente.) |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão sempre que uma operação de criptografia de disco for executada na mesma VM. |
| vmName | Nome da VM em que a operação de criptografia deve ser executada. |
| senha | Digite uma frase secreta forte como chave de criptografia de dados. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Use o recurso EncryptFormatAll para discos de dados em VMs Linux IaaS
O parâmetro EncryptFormatAll reduz o tempo de criptografia dos discos de dados do Linux. Partições que atendem a determinados critérios são formatadas (com seu sistema de arquivos atual). Então eles são remontados de volta para onde estavam antes da execução do comando. Se você quiser excluir um disco de dados que atenda aos critérios, você pode desmontá-lo antes de executar o comando.

 Depois de executar este comando, todas as unidades que foram montadas anteriormente são formatadas. Em seguida, a camada de criptografia começa em cima da unidade agora vazia. Quando essa opção é selecionada, o disco de recurso efêmero anexado à VM também é criptografado. Se a unidade efêmera for redefinida, ela será reformatada e recriptografada para o VM pela solução Azure Disk Encryption na próxima oportunidade.

>[!WARNING]
> EncryptFormatTudo não deve ser usado quando há dados necessários nos volumes de dados de uma VM. Você pode excluir discos da criptografia desmontando-os. Experimente o parâmetro EncryptFormatAll em um VM de teste primeiro para entender o parâmetro de recurso e sua implicação antes de experimentá-lo na VM de produção. A opção EncryptFormatAll formata o disco de dados, de modo que todos os dados nele serão perdidos. Antes de prosseguir, verifique se todos os discos que você deseja excluir estão devidamente desmontados. </br></br>
 >Se você definir esse parâmetro enquanto atualiza as configurações de criptografia, ele pode levar a uma reinicialização antes da criptografia real. Neste caso, você também deseja remover o disco que você não quer formatado a partir do arquivo fstab. Da mesma forma, você deve adicionar a partição que deseja forcitutero ao arquivo fstab antes de iniciar a operação de criptografia. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> Critérios do EncryptFormatAll
O parâmetro passa por todas as partições e as criptografa desde que atendam a *todos os* seguintes critérios: 
- Não é uma partição de inicialização/SO/raiz
- Ainda não está criptografado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Criptografe os discos que compõem o volume RAID ou LVM em vez do volume RAID ou LVM.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Usar o parâmetro EncryptFormatAll com um modelo
Para usar a opção EncryptFormatAll, use qualquer modelo preexistente do Azure Resource Manager que criptografe um VM Linux e altere o campo **EncryptionOperation** para o recurso AzureDiskEncryption.

1. Como exemplo, use o [modelo do Resource Manager para criptografar uma VM da IaaS do Linux em execução](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Selecione **Implantar para o Azure** no modelo de partida rápida do Azure.
3. Alterar o campo **CriptografiaOperação** de **Habilitarcriptografia** para **HabilitarCriptografia de Criptografia**.
4. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, outros parâmetros, termos legais e contrato. Selecione **Criar** para ativar a criptografia na VM IaaS existente ou em execução.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> Usar o parâmetro EncryptFormatAll com um cmdlet do PowerShell
Use o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) com o parâmetro EncryptFormatAll.

**Criptografe um VM em execução usando um segredo de cliente e EncryptFormatAll:** Como exemplo, o script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension com o parâmetro EncryptFormatAll. O grupo de recursos, VM, key vault, aplicativo Azure AD e o segredo do cliente já deveriam ter sido criados como pré-requisitos. Substitua myKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret por seus valores.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> Usar o parâmetro EncryptFormatAll com LVM (Gerenciador de Volume Lógico) 
É recomendável uma configuração LVM-on-crypt. Para todos os exemplos a seguir, substitua o caminho do dispositivo e os pontos de montagem por qualquer que seja adequado ao seu estojo de uso. Essa configuração pode ser feita da seguinte maneira:

- Adicione os discos de dados que irão compor a VM.
- Formatar, montar e adicionar esses discos ao arquivo fstab.

    1. Formate o disco adicionado recentemente. Usamos symlinks gerados pelo Azure aqui. O uso de symlinks evita problemas relacionados à alteração de nomes de dispositivos. Para obter mais informações, consulte [Problemas de nomes de dispositivos](troubleshoot-device-names-problems.md).
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Monte os discos.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Adicione ao fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Execute o Set-AzVMDiskEncryptionExtension PowerShell cmdlet com -EncryptFormatAll para criptografar esses discos.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Configure a LVM sobre esses novos discos. Observe que as unidades criptografadas serão desbloqueadas após a VM concluir a inicialização. Portanto, a montagem de LVM também deverá ser subsequentemente atrasada.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Novas VMs da IaaS criadas a partir de chaves de criptografia e VHD criptografado pelo cliente
Nesse cenário, você pode habilitar a criptografia usando o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam detalhadamente o modelo do Gerenciador de Recursos e comandos da CLI. 

Use as instruções no apêndice para preparar imagens previamente criptografadas que podem ser usadas no Azure. Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.

* [Preparar um VHD Linux previamente criptografado](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo ou fazer backup de uma instância de VM gerenciada baseada em disco fora e antes de habilitar a criptografia de disco Do Zure. Você pode tirar um instantâneo do disco gerenciado do portal ou usar o [Backup do Azure](../../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, use o cmdlet Set-AzVMDiskEncryptionExtension para criptografar discos gerenciados especificando o parâmetro -skipVmBackup. O comando Set-AzVMDiskEncryptionExtensioné o comando falha contra VMs gerenciadas baseadas em disco até que um backup seja feito e este parâmetro seja especificado. 
>
>Criptografar ou desativar a criptografia pode fazer com que a VM seja reiniciada.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Usar o Azure PowerShell para criptografar VMs da IaaS com VHDs previamente criptografados 
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)do cmShell powerShell . O exemplo a seguir lhe dá alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Habilitar criptografia em um disco de dados adicionado recentemente
Você pode adicionar um novo disco de dados usando [o az vm disk attach](add-disk.md) ou através do portal [Azure](attach-disk-portal.md). Antes de poder criptografar, primeiro será necessário montar o disco de dados anexado recentemente. Você deve solicitar a criptografia da unidade de dados porque a unidade ficará inutilizável enquanto a criptografia estiver em andamento. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Habilite a criptografia em um disco recém-adicionado com o Azure CLI
 Se a VM foi previamente criptografada com "Todos", então o parâmetro --volume-type deve permanecer todo. Todos inclui o sistema operacional e os discos de dados. Se a VM foi previamente criptografada com um tipo de volume de "SO", então o parâmetro de tipo de volume deve ser alterado para Tudo para que o SISTEMA OPERACIONAL e o novo disco de dados sejam incluídos. Se a VM foi criptografada apenas com o tipo de volume de "Dados", então ele pode permanecer dados como demonstrado aqui. Adicionar e anexar um novo disco de dados a uma VM não é preparação suficiente para criptografia. O disco recém-conectado também deve ser formatado e montado corretamente dentro da VM antes de ativar a criptografia. No Linux, o disco deve ser montado em /etc/fstab com um [nome de dispositivo de bloco persistente](troubleshoot-device-names-problems.md). 

Em contraste com a sintaxe powershell, a CLI não exige que você forneça uma versão de seqüência única quando você habilita a criptografia. A CLI gera e usa automaticamente o próprio valor de versão de sequência exclusivo.

-  **Criptografe uma VM em execução usando um segredo de cliente:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografe um VM em execução usando o KEK para envolver o segredo do cliente:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Habilitar criptografia em um disco adicionado recentemente com Azure PowerShell
 Quando você usa o Powershell para criptografar um novo disco para Linux, uma nova versão de seqüência precisa ser especificada. A versão da sequência deverá ser exclusiva. O script a seguir gera um GUID para a versão seqüencial. 
 

-  **Criptografe uma VM em execução usando um segredo de cliente:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, VM, key vault, aplicativo Azure AD e o segredo do cliente já deveriam ter sido criados como pré-requisitos. Substitua myVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret por seus valores. O parâmetro -VolumeType é configurado para discos de dados e não para o disco de SO. Se a VM foi previamente criptografada com um tipo de volume de "SO" ou "Todos", então o parâmetro -VolumeType deve ser alterado para Tudo para que o SISTEMA OPERACIONAL e o novo disco de dados sejam incluídos.

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
- **Criptografe um VM em execução usando o KEK para envolver o segredo do cliente:** O Azure Disk Encryption permite especificar uma chave existente no cofre de chaves para envolver segredos de criptografia de disco que foram gerados ao ativar a criptografia. Quando uma chave de criptografia é especificada, o Azure Disk Encryption usa essa chave para envolver os segredos de criptografia antes de escrever no cofre de chaves. O parâmetro -VolumeType é configurado para discos de dados e não para o disco de SO. Se a VM foi previamente criptografada com um tipo de volume de "SO" ou "Todos", então o parâmetro -VolumeType deve ser alterado para Tudo para que o SISTEMA OPERACIONAL e o novo disco de dados sejam incluídos.

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
> A sintaxe para o valor do parâmetro disk-encryption-keyvault é a seqüência de identificadores completos: /assinaturas/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Desabilitar criptografia para VMs do Linux
Você pode desativar a criptografia usando o Azure PowerShell, o Azure CLI ou um modelo de Gerenciador de recursos. 

>[!IMPORTANT]
>Desabilitar criptografia com Azure Disk Encryption em VMs do Linux tem suporte apenas para volumes de dados. Não é suportado em dados ou volumes do Sistema Operacional se o volume do Sistema Operacional tiver sido criptografado. 

- **Desativar criptografia de disco com o Azure PowerShell:** Para desativar a criptografia, use o [cmdlet Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Desabilitar criptografia com a CLI do Azure:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desativar criptografia com um modelo de Gerenciador de recursos:** Para desativar a criptografia, use a criptografia Desativar em um modelo [De VM Linux em execução.](https://aka.ms/decrypt-linuxvm)
     1. Selecione **Implantar para Azure**.
     2. Selecione a assinatura, o grupo de recursos, o local, a VM, os termos legais e o contrato.
     3. Selecione **Comprar** para desativar a criptografia de disco em uma VM windows em execução. 


## <a name="next-steps"></a>Próximas etapas

- [Criptografia de disco do Azure para visão geral do Linux](disk-encryption-overview-aad.md)
- [Criação e configuração de um cofre de chaves para criptografia de disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
