---
title: Versão prévia - Crie uma versão da imagem criptografada com suas próprias chaves
description: Crie uma versão da imagem em uma Galeria de Imagens Compartilhadas usando chaves de criptografia gerenciadas pelo cliente.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cynthn
ms.openlocfilehash: dd2d42bb83afa5a97bd5bd71d7b1a4bcc506d93e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976157"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Visualização: Use chaves gerenciadas pelo cliente para criptografar imagens

As imagens da galeria são armazenadas como discos gerenciados; portanto, elas são criptografadas automaticamente usando a criptografia do lado do servidor. A criptografia no servidor usa a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis e está em conformidade com FIPS 140-2. Para obter mais informações sobre os módulos criptografos subjacentes aos discos gerenciados do Azure, consulte [API de Criptografia: Next Generation](/windows/desktop/seccng/cng-portal)

Você pode contar com chaves gerenciadas por plataforma para a criptografia de suas imagens, usar suas próprias chaves ou pode usar ambas juntas para criptografia dupla. Se você optar por gerenciar a criptografia com suas próprias chaves, pode especificar uma *chave gerenciada pelo cliente* a ser usada para criptografar e descriptografar todos os discos nas suas imagens. 

A criptografia do lado do servidor que usa chaves gerenciadas pelo cliente usa o Azure Key Vault. É possível importar [as chaves RSA](../key-vault/keys/hsm-protected-keys.md) para o Key Vault ou gerar novas chaves RSA no Azure Key Vault.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo requer que você já tenha um conjunto de criptografia de disco para usar para sua imagem.

- Para usar apenas uma chave gerenciada pelo cliente, consulte **habilitar chaves gerenciadas pelo cliente com criptografia do lado do servidor** usando o [portal do Azure](./disks-enable-customer-managed-keys-portal.md) ou o [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset).

- Para usar chaves gerenciadas por plataforma e gerenciadas pelo cliente (para criptografia dupla), consulte **habilitar a criptografia dupla em repouso** usando o [portal do Azure](./disks-enable-double-encryption-at-rest-portal.md) ou o [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).
    > [!IMPORTANT]
    > Você deve usar este link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) para acessar o portal do Azure. A criptografia dupla em repouso não está visível no momento no portal do Azure público sem usar o link.

## <a name="limitations"></a>Limitações

Há várias limitações ao usar chaves gerenciadas pelo cliente para criptografar imagens da galeria de imagens compartilhadas:  

- Os conjuntos de chaves de criptografia devem estar na mesma assinatura que a imagem.

- Os conjuntos de chaves de criptografia são recursos regionais, de modo que cada região requer um conjunto de chaves de criptografia diferente.

- Não é possível compartilhar imagens que usam chaves gerenciadas pelo cliente. 

- Após usar suas próprias chaves para criptografar um disco ou uma imagem, não é possível usar novamente chaves gerenciadas pela plataforma para criptografar esses discos ou imagens.


> [!IMPORTANT]
> Atualmente, a criptografia que usa chaves gerenciadas pelo cliente está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Para a versão prévia pública, primeiro você precisa registrar o recurso.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Leva alguns minutos para o registro ser concluído. Use o Get-AzProviderFeature para verificar o status do registro do recurso.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando o RegistrationState voltar como Registrado, você poderá passar para a próxima etapa.

Verifique o registro do seu provedor. Verifique se ele volta como `Registered`.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se não voltar como `Registered`, use o seguinte para registrar os provedores:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Para especificar uma criptografia de disco definida para uma versão de imagem, use o [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) com o parâmetro `-TargetRegion`. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


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

É possível criar uma VM de uma galeria de imagens compartilhada e usar chaves gerenciadas pelo cliente para criptografar os discos. A sintaxe é a mesma que a criação de uma VM [generalizada](vm-generalized-image-version-powershell.md) ou [especializada](vm-specialized-image-version-powershell.md) a partir de uma imagem. Você precisa usar o conjunto de parâmetros estendidos e adicionar `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` à configuração da VM.

Para discos de dados, você precisa adicionar o parâmetro `-DiskEncryptionSetId $setID` ao usar o [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Para a versão prévia pública, primeiro você precisa registrar o recurso.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Verifique o status do registro do recurso.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando isso voltar como `"state": "Registered"`, você poderá passar para a próxima etapa.

Verifique seu registro.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Caso não mostre estar registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Para especificar uma criptografia de disco definida para uma versão de imagem, use a [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) com o parâmetro `--target-region-encryption`. O formato de `--target-region-encryption` é uma lista separada por vírgulas de chaves para criptografar o sistema operacional e os discos de dados. O resultado deve ser assim: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Use `--managed-image` para especificar a origem da versão da imagem se a origem do disco do sistema operacional for um disco gerenciado ou uma VM. Neste exemplo, a origem é uma imagem gerenciada que tem um disco do sistema operacional e um disco de dados no LUN 0. O disco do sistema operacional será criptografado com o DiskEncryptionSet1 e o disco de dados com o DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Use `--os-snapshot` para especificar o disco do sistema operacional se a origem do disco do sistema operacional for um instantâneo. Adicione instantâneos de disco de dados que também devem fazer parte da versão da imagem usando `--data-snapshot-luns` para especificar o LUN e `--data-snapshots` para especificar os instantâneos.

Neste exemplo, as origens são instantâneos de disco. Há um disco do sistema operacional e um disco de dados no LUN 0. O disco do sistema operacional será criptografado com o DiskEncryptionSet1 e o disco de dados com o DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Criar a VM

É possível criar uma VM de uma galeria de imagens compartilhada e usar chaves gerenciadas pelo cliente para criptografar os discos. A sintaxe é a mesma que a criação de uma VM [generalizada](vm-generalized-image-version-cli.md) ou [especializada](vm-specialized-image-version-cli.md) a partir de uma imagem. Você só precisa adicionar o parâmetro `--os-disk-encryption-set` com a ID do conjunto de criptografia. Para discos de dados, adicione `--data-disk-encryption-sets` com uma lista delimitada por espaços dos conjuntos de criptografia de disco para os discos de dados.


## <a name="portal"></a>Portal

Ao criar a versão da imagem no portal, você pode usar a guia **criptografia** para entrar em aplicar seus conjuntos de criptografia de armazenamento.

> [!IMPORTANT]
> Para usar a criptografia dupla, você deve usar este link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) para acessar o portal do Azure. A criptografia dupla em repouso não está visível no momento no portal do Azure público sem usar o link.


1. Na página **Criar uma versão de imagem**, selecione a guia **Criptografia**.
2. Em **tipo de criptografia**, selecione **criptografia em repouso com uma chave gerenciada pelo cliente** ou **criptografia dupla com chaves gerenciadas por plataforma e gerenciadas pelo cliente**. 
3. Selecione o **Conjunto de criptografia de disco** a ser usado na lista suspensa para cada disco na imagem. 

### <a name="create-the-vm"></a>Criar a VM

Você pode criar uma VM com base em uma versão de imagem e usar chaves gerenciadas pelo cliente para criptografar os discos. Quando você cria a VM no portal, na guia **discos** , selecione **criptografia em repouso com chaves gerenciadas pelo cliente** ou **criptografia dupla com chaves** gerenciadas por plataforma e gerenciadas pelo cliente para o **tipo de criptografia**. Depois selecione o conjunto de criptografia na lista suspensa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Criptografia de disco do lado do servidor](./windows/disk-encryption.md).

Para obter informações sobre como fornecer informações do plano de compra, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).
