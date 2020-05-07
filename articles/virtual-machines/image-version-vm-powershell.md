---
title: Criar uma imagem de uma VM (versão prévia)
description: Saiba como usar Azure PowerShell para criar uma imagem em uma galeria de imagens compartilhada de uma VM existente no Azure.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 454ed810f950924d3dd790a2442fe29816bf940d
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838460"
---
# <a name="preview-create-an-image-from-a-vm"></a>Visualização: criar uma imagem de uma VM

Se você tiver uma VM existente que deseja usar para criar várias VMs idênticas, poderá usar essa VM para criar uma imagem em uma galeria de imagens compartilhadas usando Azure PowerShell. Você também pode criar uma imagem de uma VM usando o [CLI do Azure](image-version-vm-cli.md).

Você pode capturar uma imagem de VMs [especializadas e generalizadas](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) usando Azure PowerShell. 

As imagens em uma galeria de imagens têm dois componentes, que serão criados neste exemplo:
- Uma **definição de imagem** contém informações sobre a imagem e os requisitos para usá-la. Isso inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é usada para criar uma VM ao usar uma galeria de imagens compartilhada. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Quando você cria uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você deve ter uma galeria de imagens compartilhada existente e uma VM existente no Azure para usar como a origem. 

Se a VM tiver um disco de dados anexado, o tamanho do disco de dados não poderá ser maior que 1 TB.

Ao trabalhar com este artigo, substitua os nomes de recursos onde necessário.


## <a name="get-the-gallery"></a>Obter a Galeria

Você pode listar todas as galerias e definições de imagem por nome. Os resultados estão no formato `gallery\image definition\image version`.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Depois de encontrar a Galeria correta e as definições de imagem, crie variáveis para que elas sejam usadas mais tarde. Este exemplo obtém a galeria chamada *myGallery* no grupo de recursos *MyResource* Group.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>Obter a VM

Você pode ver uma lista de VMs que estão disponíveis em um grupo de recursos usando [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Depois de saber o nome da VM e em qual grupo de recursos ele está, você `Get-AzVM` pode usar novamente para obter o objeto da VM e armazená-lo em uma variável para uso posterior. Este exemplo obtém uma VM chamada *sourceVM* do grupo de recursos "MyResource Group" e a atribui à variável *$sourceVm*. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

É uma prática recomendada stop\deallocate a VM antes de criar uma imagem usando [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

Definições de imagem crie um agrupamento lógico para imagens. Eles são usados para gerenciar informações sobre a imagem. Os nomes de definição de imagem podem ser compostos de letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos. 

Ao fazer a definição de imagem, verifique se o tem todas as informações corretas. Se você generaliza a VM (usando Sysprep para Windows ou waagent-deprovision para Linux), você deve criar uma definição de imagem usando `-OsState generalized`. Se você não tiver generalizado a VM, crie uma definição de `-OsState specialized`imagem usando.

Para obter mais informações sobre os valores que você pode especificar para uma definição de imagem, consulte [definições de imagem](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Crie a definição de imagem usando [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Neste exemplo, a definição de imagem é chamada *myImageDefinition*e é para uma VM especializada que executa o Windows. Para criar uma definição para imagens usando o Linux, `-OsType Linux`use. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem usando [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

Neste exemplo, a versão da imagem é *1.0.0* e ela é replicado para os datacenters *Centro-Oeste dos EUA* e *Centro-Sul dos EUA*. Ao escolher regiões de destino para replicação, lembre-se de que você também precisa incluir a região de *origem* como um destino para replicação.

Para criar uma versão de imagem da VM, use `$vm.Id.ToString()` para o `-Source`.

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Pode levar um tempo para replicar a imagem para todas as regiões de destino, portanto, criamos um trabalho para podermos acompanhar o progresso. Para ver o andamento do trabalho, digite `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Você precisa aguardar que a versão da imagem termine completamente de ser compilada e replicada antes de poder usar a mesma imagem gerenciada para criar outra versão de imagem.
>
> Você também pode armazenar sua imagem no armazenamento Premiun por um armazenamento `-StorageAccountType Premium_LRS`com [redundância de zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) ou adição, `-StorageAccountType Standard_ZRS` adicionando ao criar a versão da imagem.
>

## <a name="next-steps"></a>Próximas etapas

Depois de verificar se a nova versão da imagem está funcionando corretamente, você pode criar uma VM. Crie uma VM com base em uma [versão de imagem especializada](vm-specialized-image-version-powershell.md) ou em uma [versão de imagem generalizada](vm-generalized-image-version-powershell.md).
