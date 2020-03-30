---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67171885"
---
## <a name="update-resources"></a>Atualizar recursos

Existem algumas limitações sobre o que pode ser atualizado. Os seguintes itens podem ser atualizados: 

Galeria de imagens compartilhadas:
- Descrição

definição da imagem:
- vCPUs recomendadas
- Memória recomendada
- Descrição
- Data de fim da vida útil

Versão da imagem:
- Contagem de réplicas regionais
- Regiões de destino
- Exclusão da mais recente
- Data de fim da vida útil

Se você planeja adicionar regiões de réplica, não exclua a imagem gerenciada de origem. A imagem gerenciada de origem é necessária para replicar a versão de imagem para regiões adicionais. 

Para atualizar a descrição de uma galeria, use [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Este exemplo mostra como usar [update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) para atualizar a data de fim de vida para nossa definição de imagem.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Este exemplo mostra como usar [update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) para excluir esta versão de imagem de ser usada como a imagem *mais recente.*

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Limpar recursos

Ao excluir recursos, você precisa começar com o último item nos recursos aninhados - a versão de imagem. Uma vez que as versões são excluídas, você pode excluir a definição de imagem. Você não pode excluir a galeria até que todos os recursos abaixo dela tenham sido excluídos.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

