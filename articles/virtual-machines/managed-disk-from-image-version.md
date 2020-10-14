---
title: Criar um disco gerenciado com base em uma versão de imagem
description: Crie um disco gerenciado com base em uma versão de imagem em uma galeria de imagens compartilhada.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: bf4a1feb91a1ac4b0bca0d6afdbac41a8be3aa4f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049622"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Criar um disco gerenciado com base em uma versão de imagem

Se necessário, você pode criar um disco gerenciado de uma versão de imagem armazenada em uma galeria de imagens compartilhada.


## <a name="cli"></a>CLI

Defina a `source` variável como a ID da versão da imagem e, em seguida, use [AZ Disk Create](/cli/azure/disk.md#az_disk_create) para criar o disco gerenciado. 


Você pode ver uma lista de versões de imagem usando [AZ SIG Image-Version List](/cli/azure/sig/image-version.md#az_sig_image_version_list). Neste exemplo, estamos procurando todas as versões de imagem que fazem parte da definição de imagem *myImageDefinition* na Galeria de imagens *myGallery* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


Neste exemplo, criamos um disco gerenciado chamado *myManagedDisk*, na região *eastus* , em um grupo de recursos chamado *MyResource*Group. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Se o disco for um disco de dados, adicione `--gallery-image-reference-lun` para especificar o LUN.

## <a name="powershell"></a>PowerShell

Você pode listar todas as versões de imagem usando [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



Depois de ter todas as informações necessárias, você pode usar [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) para obter a versão da imagem de origem que deseja usar e atribuí-la a uma variável. Neste exemplo, estamos obtendo a versão da `1.0.0` imagem, da `myImageDefinition` definição, na `myGallery` Galeria de origem, no `myResourceGroup` grupo de recursos.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Configure algumas variáveis para as informações de disco.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Crie uma configuração de disco e, em seguida, o disco usando a ID de versão da imagem de origem. Para o `-GalleryImageReference` , o LUN será necessário apenas se a origem for um disco de dados.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Crie o disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Próximas etapas

Você também pode criar uma versão de imagem de um disco gerenciado usando o [CLI do Azure](image-version-managed-image-cli.md) ou o [PowerShell](image-version-managed-image-powershell.md).


