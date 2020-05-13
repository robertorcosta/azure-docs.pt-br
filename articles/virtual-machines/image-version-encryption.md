---
title: Visualização-criar uma versão de imagem criptografada com suas próprias chaves
description: Crie uma versão de imagem em uma galeria de imagens compartilhadas, usando chaves de criptografia gerenciadas pelo cliente.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/06/2020
ms.author: cynthn
ms.openlocfilehash: aeacfdc07e5349dfce45b209da1d78bddf870f33
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83269573"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Versão prévia: usar chaves gerenciadas pelo cliente para criptografar imagens

As imagens da galeria são armazenadas como discos gerenciados, portanto, são criptografadas automaticamente usando a criptografia do lado do servidor. A criptografia do lado do servidor usa a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. Para obter mais informações sobre os módulos de criptografia subjacentes ao Azure Managed disks, consulte [API de criptografia: próxima geração](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

Você pode contar com chaves gerenciadas por plataforma para a criptografia de suas imagens ou pode gerenciar a criptografia usando suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, poderá especificar uma *chave gerenciada pelo cliente* a ser usada para criptografar e descriptografar todos os discos em suas imagens. 

A criptografia do lado do servidor usando chaves gerenciadas pelo cliente usa Azure Key Vault. Você pode importar [suas chaves RSA](../key-vault/keys/hsm-protected-keys.md) para seu Key Vault ou gerar novas chaves rsa no Azure Key Vault.

Para usar chaves gerenciadas pelo cliente para imagens, primeiro você precisa de um Azure Key Vault. Em seguida, você cria um conjunto de criptografia de disco. O conjunto de criptografia de disco é usado durante a criação de suas versões de imagem.

Para obter mais informações sobre como criar e usar conjuntos de criptografia de disco, consulte [chaves gerenciadas pelo cliente](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

## <a name="limitations"></a>Limitações

Há várias limitações ao usar chaves gerenciadas pelo cliente para criptografar imagens da Galeria de imagens compartilhadas:  

- Os conjuntos de chaves de criptografia devem estar na mesma assinatura e região que você imagem.

- Não é possível compartilhar imagens que usam chaves gerenciadas pelo cliente. 

- Não é possível replicar imagens que usam chaves gerenciadas pelo cliente para outras regiões.

- Depois de usar suas próprias chaves para criptografar um disco ou uma imagem, você não pode voltar a usar chaves gerenciadas pela plataforma para criptografar esses discos ou imagens.


> [!IMPORTANT]
> A criptografia usando chaves gerenciadas pelo cliente está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Para a visualização pública, primeiro você precisa registrar o recurso.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Demora alguns minutos para que o registro seja concluído. Use Get-AzProviderFeature para verificar o status do registro do recurso.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando o Registrostate retorna registrado, você pode passar para a próxima etapa.

Verifique o registro do provedor. Certifique-se de que ele retorna `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se ele não retornar `Registered` , use o seguinte para registrar os provedores:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Para especificar uma criptografia de disco definida como para uma versão de imagem, use [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) com o `-TargetRegion` parâmetro. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @{$region1}


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Criar uma máquina virtual

Você pode criar uma VM de uma galeria de imagens compartilhada e usar chaves gerenciadas pelo cliente para criptografar os discos. A sintaxe é a mesma de criar uma VM [generalizada](vm-generalized-image-version-powershell.md) ou [especializada](vm-specialized-image-version-powershell.md) a partir de uma imagem, você precisa usar o conjunto de parâmetros estendidos e adicionar `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` à configuração da VM.

Para discos de dados, você precisa adicionar o `-DiskEncryptionSetId $setID` parâmetro ao usar [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Para a visualização pública, primeiro você precisa registrar o recurso.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Verifique o status do registro do recurso.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando isso retornar `"state": "Registered"` , você poderá passar para a próxima etapa.

Verifique seu registro.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Se não disser registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Para especificar uma criptografia de disco definida como para uma versão de imagem, use [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create) com o `--target-region-encryption` parâmetro. O formato para o `--target-region-encryption` é uma lista separada por espaços de chaves para criptografar o sistema operacional e os discos de dados. O resultado deve ser assim: `<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>`. 

Se a origem do disco do sistema operacional for um disco gerenciado ou uma VM, use `--managed-image` para especificar a origem da versão da imagem. Neste exemplo, a origem é uma imagem gerenciada que tem um disco do sistema operacional, bem como um disco de dados no LUN 0. O disco do sistema operacional será criptografado com DiskEncryptionSet1 e o disco de dados será criptografado com DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Se a origem do disco do sistema operacional for um instantâneo, use `--os-snapshot` para especificar o disco do sistema operacional. Se houver instantâneos de disco de dados que também devem fazer parte da versão da imagem, adicione-os usando `--data-snapshot-luns` para especificar o LUN e `--data-snapshots` para especificar os instantâneos.

Neste exemplo, as fontes são instantâneos de disco. Há um disco do sistema operacional e também um disco de dados no LUN 0. O disco do sistema operacional será criptografado com DiskEncryptionSet1 e o disco de dados será criptografado com DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot"
   --data-snapshot-luns 0
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot"
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Criar a VM

Você pode criar uma VM de uma galeria de imagens compartilhada e usar chaves gerenciadas pelo cliente para criptografar os discos. A sintaxe é a mesma de criar uma VM [generalizada](vm-generalized-image-version-cli.md) ou [especializada](vm-specialized-image-version-cli.md) a partir de uma imagem, basta adicionar o `--os-disk-encryption-set` parâmetro com a ID do conjunto de criptografia. Para discos de dados, adicione `--data-disk-encryption-sets` com uma lista delimitada por espaço dos conjuntos de criptografia de disco para os discos de dados.


## <a name="portal"></a>Portal

Ao criar a versão da imagem no portal, você pode usar a guia **criptografia** para inserir as informações sobre seus conjuntos de criptografia de armazenamento.

1. Na página **criar uma imagem de versão** , selecione a guia **criptografia** .
2. Em **tipo de criptografia**, selecione **criptografia em repouso com uma chave gerenciada pelo cliente**. 
3. Para cada disco na imagem, selecione o **conjunto de criptografia de disco** a ser usado na lista suspensa. 

### <a name="create-the-vm"></a>Criar a VM

Você pode criar uma VM de uma galeria de imagens compartilhada e usar chaves gerenciadas pelo cliente para criptografar os discos. Ao criar a VM no portal, na guia **discos** , selecione **criptografia em repouso com chaves gerenciadas pelo cliente** para o **tipo de criptografia**. Em seguida, você pode selecionar o conjunto de criptografia na lista suspensa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [criptografia de disco do lado do servidor](/windows/disk-encryption.md)).
