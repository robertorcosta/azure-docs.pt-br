---
title: Cenários de Azure Disk Encryption em VMs Linux
description: Este artigo fornece instruções sobre como habilitar a criptografia de disco Microsoft Azure para VMs Linux para vários cenários
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0a1515144f340938cddfd5ca9f2ac4803bcb3f77
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174714"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Cenários de Azure Disk Encryption em VMs Linux

Azure Disk Encryption usa o recurso DM-cript do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados das VMs (máquinas virtuais) do Azure e é integrado com o Azure Key Vault para ajudá-lo a controlar e gerenciar as chaves e os segredos de criptografia de disco. Para obter uma visão geral do serviço, consulte [Azure Disk Encryption para VMs do Linux](disk-encryption-overview.md).

Há muitos cenários de criptografia de disco, e as etapas podem variar de acordo com o cenário. As seções a seguir abrangem os cenários mais detalhadamente para VMs do Linux.

Você só pode aplicar a criptografia de disco a máquinas virtuais de [tamanhos de VM e sistemas operacionais com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems). Você também deve atender aos seguintes pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chave de criptografia](disk-encryption-overview.md#encryption-key-storage-requirements)

Em todos os casos, você deve [fazer um instantâneo](snapshot-copy-managed-disk.md) e/ou criar um backup antes que os discos sejam criptografados. Os backups garantem que uma opção de recuperação seja possível, no caso de uma falha inesperada durante a criptografia. VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. Depois que um backup é feito, você pode usar o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte o artigo [Backup do Microsoft Azure](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Se você tiver usado anteriormente Azure Disk Encryption com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM. Consulte [Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter detalhes. 
>
> - Ao criptografar volumes do sistema operacional Linux, a VM deve ser considerada indisponível. É altamente recomendável evitar logons SSH enquanto a criptografia estiver em andamento para evitar problemas ao bloquear os arquivos abertos que precisarão ser acessados durante o processo de criptografia. Para verificar o progresso, use o cmdlet do PowerShell [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) ou o comando [VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI. Esse processo deve levar algumas horas para um volume do sistema operacional de 30 GB, mais algum tempo para criptografar volumes de dados. O tempo para criptografia de volume de dados será proporcional ao tamanho e à quantidade dos volumes de dados, a menos que a opção EncryptFormatAll seja usada. 
> - Desabilitar criptografia nas VMs do Linux tem suporte apenas para volumes de dados. Não haverá suporte em dados ou volumes de SO, se o volume de SO tiver sido criptografado.  

## <a name="install-tools-and-connect-to-azure"></a>Instalar ferramentas e conectar-se ao Azure

Azure Disk Encryption pode ser habilitado e gerenciado por meio do [CLI do Azure](/cli/azure) e [Azure PowerShell](/powershell/azure/new-azureps-module-az). Para fazer isso, você deve instalar as ferramentas localmente e conectar-se à sua assinatura do Azure.

### <a name="azure-cli"></a>CLI do Azure

O [CLI 2.0 do Azure](/cli/azure) é uma ferramenta de linha de comando para gerenciar recursos do Azure. A CLI é projetada para consultar dados com flexibilidade, dar suporte a operações de longa execução como processos desbloqueados e facilitar o script. Você pode instalá-lo localmente seguindo as etapas em [instalar o CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para [entrar em sua conta do Azure com o CLI do Azure](/cli/azure/authenticate-azure-cli), use o comando [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Se você deseja selecionar um locatário a ser usado para entrar, use:
    
```azurecli
az login --tenant <tenant>
```

Se você tiver várias assinaturas e quiser especificar uma lista específica, obtenha a lista de assinaturas com [az account list](/cli/azure/account#az-account-list) e especifique com [az account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Para obter mais informações, consulte [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
O [módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) fornece um conjunto de cmdlets que usa o modelo de [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) para gerenciar seus recursos do Azure. Você pode usá-lo em seu navegador com [Azure cloud Shell](../../cloud-shell/overview.md), ou pode instalá-lo em seu computador local usando as instruções em [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). 

Se você já tiver instalado localmente, verifique se que você usar a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Para [entrar em sua conta do Azure com Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), use o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Se você tiver várias assinaturas e quiser especificar uma, use o cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) para listá-las, seguidos do cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Executar o cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verificará se a assinatura correta foi selecionada.

Para confirmar se os cmdlets Azure Disk Encryption estão instalados, use o cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Para obter mais informações, consulte [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Habilitar a criptografia em uma VM Linux existente ou em execução
Nesse cenário, é possível habilitar a criptografia usando o modelo do Resource Manager, os cmdlets do PowerShell ou os comandos da CLI. Se você precisar de informações de esquema para a extensão de máquina virtual, consulte o [Azure Disk Encryption para extensão do Linux](../extensions/azure-disk-enc-linux.md) artigo.

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser extraído do portal ou por meio do [backup do Azure](../../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzVMDiskEncryptionExtension pode ser usado para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. O comando Set-AzVMDiskEncryptionExtension irá falhar em VMs baseadas em disco gerenciado até que um backup tenha sido feito e esse parâmetro tenha sido especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Habilitar a criptografia em uma VM Linux existente ou em execução usando CLI do Azure 

Você pode habilitar a criptografia de disco em seu VHD criptografado Instalando e usando a ferramenta de linha de comando [CLI do Azure](/cli/azure/?view=azure-cli-latest) . Você pode usá-lo em seu navegador com o [Azure Cloud Shell](../../cloud-shell/overview.md), ou você pode instalá-lo em seu computador local e usá-lo em qualquer sessão do PowerShell. Para habilitar a criptografia em VMs Linux existentes ou em execução no Azure, use os seguintes comandos da CLI:

Use o comando [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) para habilitar a criptografia em uma máquina virtual em execução no Azure.

- **Criptografar uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografar uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificar se os discos estão criptografados:** Para verificar o status de criptografia de uma VM, use o comando [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desabilitar criptografia:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Habilitar a criptografia em uma VM Linux existente ou em execução usando o PowerShell
Use o cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual em execução no Azure. Faça um [instantâneo](snapshot-copy-managed-disk.md) e/ou faça backup da VM com o [backup do Azure](../../backup/backup-azure-vms-encryption.md) antes que os discos sejam criptografados. O parâmetro-skipVmBackup já está especificado nos scripts do PowerShell para criptografar uma VM do Linux em execução.

-  **Criptografar uma VM em execução:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o cofre de chaves foram criados como pré-requisitos. Substitua MyVirtualMachineResourceGroup, MySecureVM e MySecureVault pelos seus valores. Modifique o parâmetro-Volumetype para especificar quais discos você está criptografando.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Criptografar uma VM em execução usando KEK:** Talvez seja necessário adicionar o parâmetro -VolumeType se você estiver criptografando discos de dados e não o disco de SO. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Verificar se os discos estão criptografados:** Para verificar o status de criptografia de uma VM, use o cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desabilitar criptografia de disco:** Para desabilitar a criptografia, use o cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Habilitar a criptografia em uma VM do Linux existente ou em execução com um modelo

Você pode habilitar a criptografia de disco em uma VM Linux existente ou em execução no Azure usando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Clique em **Implantar no Azure** no modelo de início rápido do Azure.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **criar** para habilitar a criptografia na VM existente ou em execução.

A tabela a seguir lista os parâmetros de modelo do Resource Manager existente para VMs em execução ou existentes:

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| vmName | Nome da VM para executar a operação de criptografia. |
| keyVaultName | Nome do cofre de chaves para o qual a chave de criptografia deve ser carregada. Você pode obtê-lo usando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou o comando CLI do Azure `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`.|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o cofre de chaves. |
|  keyEncryptionKeyURL | URL da chave de criptografia de chave usada para criptografar a chave de criptografia. Esse parâmetro será opcional se você selecionar **nokek** na lista suspensa UseExistingKek. Se selecionar **kek** na lista suspensa UseExistingKek, você deverá inserir o valor _keyEncryptionKeyURL_. |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Os valores válidos são _OS_, _Data_ e _All_. 
| forceUpdateTag | Passe um valor exclusivo como um GUID sempre que a execução da operação precise ser forçada. |
| resizeOSDisk | A partição do SO deve ser redimensionada para ocupar o VHD do SO completo antes de dividir o volume do sistema. |
| location | Local de todos os recursos. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Usar o recurso EncryptFormatAll para discos de dados em VMs do Linux

O parâmetro **EncryptFormatAll** reduz o tempo de criptografia dos discos de dados do Linux. As partições que atendem a determinados critérios serão formatadas (com seu sistema de arquivos atual) e remontadas de volta para onde estavam antes da execução do comando. Se você quiser excluir um disco de dados que atenda aos critérios, será possível desmontá-lo antes de executar o comando.

 Depois de executar esse comando, todas as unidades que foram montadas anteriormente serão formatadas e a camada de criptografia será iniciada sobre a unidade agora vazia. Quando essa opção for selecionada, o disco de recurso efêmero anexado à VM também será criptografado. Se a unidade temporária for reiniciada, ela será reformatada e criptografada novamente para a VM pela solução do Azure Disk Encryption na próxima oportunidade. Depois que o disco de recursos for criptografado, o [agente do Microsoft Azure Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) não poderá gerenciar o disco de recursos e habilitar o arquivo de permuta, mas você poderá configurar manualmente o arquivo de permuta.

>[!WARNING]
> O EncryptFormatAll não deverá ser usado quando houver dados necessários nos volumes de dados de uma VM. É possível excluir discos da criptografia, desmontando-os. Primeiro será necessário testar o EncryptFormatAll, primeiro em uma VM de teste, e compreender o parâmetro de recurso e a respectiva implicação antes de testá-lo na VM de produção. A opção EncryptFormatAll formata o disco de dados e todos os dados nele serão perdidos. Antes de prosseguir, verifique se os discos que deseja excluir estão corretamente desmontados. </br></br>
 >Se você estiver configurando esse parâmetro ao atualizar as configurações de criptografia, isso poderá levar a um reinício antes da criptografia real. Nesse caso, é recomendável também remover o disco que você não quer que seja formatado no arquivo fstab. Da mesma forma, é necessário adicionar a partição que deseja criptografar ao arquivo fstab antes de iniciar a operação de criptografia. 

### <a name="encryptformatall-criteria"></a>Critérios de EncryptFormatAll
O parâmetro passa por todas as partições e criptografa-as desde que atendam a **todos** os critérios abaixo: 
- Não é uma partição de inicialização/SO/raiz
- Ainda não está criptografado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Criptografe os discos que compõem o volume RAID ou LVM em vez do volume RAID ou LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Use o parâmetro EncryptFormatAll com CLI do Azure
Use o comando [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual em execução no Azure.

-  **Criptografe uma VM em execução usando EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Usar o parâmetro EncryptFormatAll com um cmdlet do PowerShell
Use o cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) com o parâmetro EncryptFormatAll. 

**Criptografe uma VM em execução usando EncryptFormatAll:** Por exemplo, o script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension com o parâmetro EncryptFormatAll. O grupo de recursos, a VM e o cofre de chaves foram criados como pré-requisitos. Substitua MyVirtualMachineResourceGroup, MySecureVM e MySecureVault pelos seus valores.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Usar o parâmetro EncryptFormatAll com o LVM (Gerenciador de volumes lógicos) 
É recomendável uma configuração LVM-on-crypt. Para todos os exemplos a seguir, substitua o caminho do dispositivo e os pontos de montagem pelo que melhor adequar-se ao seu caso de uso. Essa configuração pode ser feita da seguinte maneira:

- Adicione os discos de dados que irão compor a VM.
- Formatar, montar e adicionar esses discos ao arquivo fstab.

    1. Formate o disco adicionado recentemente. Usamos symlinks gerados pelo Azure aqui. O uso de symlinks evita problemas relacionados à alteração de nomes de dispositivos. Para obter mais informações, consulte o artigo [Solucionar problemas de Nomes do Dispositivo](troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Monte os discos.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Adicione ao fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Execute o cmdlet do PowerShell Set-AzVMDiskEncryptionExtension com-EncryptFormatAll para criptografar esses discos.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Configure a LVM sobre esses novos discos. Observe que as unidades criptografadas serão desbloqueadas após a VM concluir a inicialização. Portanto, a montagem de LVM também deverá ser subsequentemente atrasada.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Novas VMs criadas a partir do VHD criptografado pelo cliente e chaves de criptografia
Nesse cenário, é possível habilitar a criptografia usando cmdlets do PowerShell ou comandos da CLI. 

Use as instruções nos mesmos scripts do Azure Disk Encryption para preparar imagens previamente criptografadas que podem ser usadas no Azure. Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.

* [Preparar um VHD Linux previamente criptografado](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzVMDiskEncryptionExtension pode ser usado para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. O comando Set-AzVMDiskEncryptionExtension irá falhar em VMs baseadas em disco gerenciado até que um backup tenha sido feito e esse parâmetro tenha sido especificado. 
>
> Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Usar Azure PowerShell para criptografar VMs com VHDs previamente criptografados 
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o cmdlet do PowerShell [set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). O exemplo abaixo fornece alguns parâmetros comuns. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Habilitar criptografia em um disco de dados adicionado recentemente

É possível adicionar um novo disco de dados usando [az vm disk attach](add-disk.md) ou [por meio do portal do Azure](attach-disk-portal.md). Antes de poder criptografar, primeiro será necessário montar o disco de dados anexado recentemente. É necessário solicitar a criptografia da unidade de dados, pois a unidade ficará inutilizável enquanto a criptografia estiver em andamento. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Habilitar criptografia em um disco adicionado recentemente com CLI do Azure

 Se a VM tiver sido criptografada anteriormente com "All", o parâmetro--volume-Type deverá permanecer como "All". Todos inclui o sistema operacional e os discos de dados. Se a VM tiver sido criptografada anteriormente com um tipo de volume de "os", o parâmetro--volume-Type deverá ser alterado para "All" para que o so e o novo disco de dados sejam incluídos. Se a VM foi criptografada apenas com o tipo de volume "Dados", ela poderá permanecer como "Dados", conforme demonstrado abaixo. Adicionar e anexar um novo disco de dados a uma VM não é preparação suficiente para criptografia. O disco anexado recentemente também deve ser formatado e montado adequadamente na VM antes de habilitar a criptografia. No Linux, o disco deve ser montado em /etc/fstab com um [nome do dispositivo de bloco persistente](troubleshoot-device-names-problems.md).  

Em contraste com a sintaxe do Powershell, a CLI não exige que o usuário forneça uma versão de sequência exclusiva ao habilitar a criptografia. A CLI gera e usa automaticamente o próprio valor de versão de sequência exclusivo.

-  **Criptografe volumes de dados de uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografe volumes de dados de uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Habilitar criptografia em um disco adicionado recentemente com Azure PowerShell
 Ao usar o Powershell para criptografar um novo disco para Linux, uma nova versão da sequência deverá ser especificada. A versão da sequência deverá ser exclusiva. O script abaixo gera um GUID para a versão da sequência. Faça um [instantâneo](snapshot-copy-managed-disk.md) e/ou faça backup da VM com o [backup do Azure](../../backup/backup-azure-vms-encryption.md) antes que os discos sejam criptografados. O parâmetro-skipVmBackup já está especificado nos scripts do PowerShell para criptografar um disco de dados recém-adicionado.
 

-  **Criptografe volumes de dados de uma VM em execução:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos. Substitua MyVirtualMachineResourceGroup, MySecureVM e MySecureVault pelos seus valores. Valores aceitáveis para o parâmetro do tipo de volume são Todos, SO e Dados. Se a VM tiver sido criptografada anteriormente com um tipo de volume de "os" ou "All", o parâmetro-Volumetype deverá ser alterado para "All" para que o so e o novo disco de dados sejam incluídos.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Criptografe volumes de dados de uma VM em execução usando KEK:** Valores aceitáveis para o parâmetro do tipo de volume são Todos, SO e Dados. Se a VM foi criptografada anteriormente com um tipo de volume de "OS" ou “ALL”, então o parâmetro -VolumeType deverá ser alterado para All para que ambos o sistema operacional e o novo disco de dados sejam incluídos.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[Subscription-ID-GUID]/resourceGroups/[KVresource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keyvault-Name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Desabilitar criptografia para VMs do Linux
É possível desabilitar a criptografia usando o Azure PowerShell, a CLI do Azure ou com um modelo do Resource Manager. 

>[!IMPORTANT]
>Desabilitar criptografia com Azure Disk Encryption em VMs do Linux tem suporte apenas para volumes de dados. Não haverá suporte em dados ou volumes de SO, se o volume de SO tiver sido criptografado.  

- **Desabilitar a criptografia de disco com o Azure PowerShell:** Para desabilitar a criptografia, use o cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Desabilitar a criptografia com a CLI do Azure:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desabilitar criptografia com um modelo do Resource Manager:** Use o modelo [Desabilitar a criptografia em uma VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) para desabilitar a criptografia.
     1. Clique em **Implantar no Azure**.
     2. Selecione a assinatura, o grupo de recursos, o local, a VM, os termos legais e o contrato.

## <a name="unsupported-scenarios"></a>Cenários sem suporte

Azure Disk Encryption não funciona para os seguintes cenários, recursos e tecnologia do Linux:

- Criptografia de VM de camada básica ou VMs criadas por meio do método de criação de VM clássico.
- Desabilitar a criptografia em uma unidade do sistema operacional ou unidade de dados de uma VM do Linux quando a unidade do sistema operacional é criptografada.
- Criptografando a unidade do sistema operacional para conjuntos de dimensionamento de máquinas virtuais do Linux.
- Criptografar imagens personalizadas em VMs do Linux.
- Integração com um sistema de gerenciamento de chaves local.
- Arquivos do Azure (sistema de arquivo compartilhado).
- NFS (Network File System).
- Volumes dinâmicos.
- Discos do sistema operacional efêmero.
- Criptografia de sistemas de arquivos compartilhados/distribuídos como (mas não se limitando a): DFS, GFS, DRDB e CephFS.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Azure Disk Encryption scripts de exemplo](disk-encryption-sample-scripts.md)
- [Solução de problemas Azure Disk Encryption](disk-encryption-troubleshooting.md)
