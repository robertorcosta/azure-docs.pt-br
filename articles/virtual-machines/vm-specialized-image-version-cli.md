---
title: Criar uma VM com base em uma versão de imagem especializada usando o CLI do Azure
description: Crie uma VM usando uma versão de imagem especializada em uma galeria de imagens compartilhada usando o CLI do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1ccf03deee2a2f72c1eb2008e1acc5bf67d16447
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796766"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Criar uma VM usando uma versão de imagem especializada com o CLI do Azure

Crie uma VM com base em uma [versão de imagem especializada](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) armazenada em uma galeria de imagens compartilhada. Se desejar criar uma VM usando uma versão de imagem generalizada, consulte [criar uma VM com base em uma versão de imagem generalizada](vm-generalized-image-version-cli.md).

Substitua os nomes de recursos conforme necessário neste exemplo. 

Liste as definições de imagem em uma galeria usando [AZ SIG Image-Definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list) para ver o nome e a ID das definições.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Crie a VM usando [AZ VM Create](/cli/azure/vm#az-vm-create) usando o parâmetro--especializado para indicar que a imagem é uma imagem especializada. 

Use a ID de definição de `--image` imagem para para criar a VM a partir da versão mais recente da imagem que está disponível. Você também pode criar a VM de uma versão específica fornecendo a ID de versão da imagem para `--image`. 

Neste exemplo, estamos criando uma VM a partir da versão mais recente da imagem *myImageDefinition* .

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Próximas etapas
O [Construtor de imagens do Azure (visualização)](./linux/image-builder-overview.md) pode ajudar a automatizar a criação da versão da imagem, até mesmo usá-la para atualizar e [criar uma nova versão da imagem a partir de uma versão de imagem existente](./linux/image-builder-gallery-update-image-version.md). 

Você também pode criar um recurso de Galeria de imagens compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM por meio de uma versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


