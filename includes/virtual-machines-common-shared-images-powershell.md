---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241220"
---
## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell [https://shell.azure.com/powershell](https://shell.azure.com/powershell)em uma guia de navegador separada indo para . Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.


## <a name="get-the-managed-image"></a>Obter a imagem gerenciada

Você pode ver uma lista de imagens que estão disponíveis em um grupo de recursos usando [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Depois que você souber o nome da imagem e em qual grupo de recursos ela está, poderá usar `Get-AzImage` novamente para obter o objeto de imagem e armazená-lo em uma variável para uso posterior. Este exemplo obtém uma imagem chamada *myImage* do grupo de recursos "myResourceGroup" e o atribui à variável *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da galeria não pode conter traços. Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

Crie uma galeria de imagens usando [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). O exemplo a seguir cria uma galeria chamada *myGallery* no grupo de recursos *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

Definições de imagem criam um agrupamento lógico para imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criadas dentro delas. Os nomes de definição de imagem podem ser compostos de letras maiúsculas ou minúsculas, dígitos, pontilhados, traços e períodos. Para obter mais informações sobre os valores que você pode especificar para uma definição de imagem, consulte [Definições de imagem](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Crie a definição de imagem usando [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a imagem da galeria é denominada *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem a partir de uma imagem gerenciada usando [new-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

Neste exemplo, a versão da imagem é *1.0.0* e ela é replicado para os datacenters *Centro-Oeste dos EUA* e *Centro-Sul dos EUA*. Ao escolher regiões-alvo para replicação, lembre-se de que você também precisa incluir a região *de origem* como um alvo para replicação.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Pode levar um tempo para replicar a imagem para todas as regiões de destino, portanto, criamos um trabalho para podermos acompanhar o progresso. Para ver o andamento do trabalho, digite `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Você precisa esperar que a versão da imagem termine completamente de ser construída e replicada antes de poder usar a mesma imagem gerenciada para criar outra versão de imagem. 
>
> Você também pode armazenar sua versão de `-StorageAccountType Standard_ZRS` imagem no Zone Redundant [Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando quando você cria a versão de imagem.
>


## <a name="share-the-gallery"></a>Compartilhe a galeria

Recomendamos que você compartilhe o acesso ao nível da galeria de imagens. Use um endereço de e-mail e o cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) para obter o ID do objeto para o usuário e, em seguida, use [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) para dar-lhes acesso à galeria. Substitua o alinne_montes@contoso.com e-mail de exemplo, neste exemplo, por suas próprias informações.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```