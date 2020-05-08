---
title: Criar uma imagem de uma VM
description: Saiba como criar uma imagem em uma galeria de imagens compartilhadas de uma VM no Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f53a6b63c744b0e3e41f7ad22270cd842da57674
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796571"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Criar uma versão de imagem de uma VM no Azure usando o CLI do Azure

Se você tiver uma VM existente que deseja usar para criar várias VMs idênticas, poderá usar essa VM para criar uma imagem em uma galeria de imagens compartilhadas usando o CLI do Azure. Você também pode criar uma imagem de uma VM usando [Azure PowerShell](image-version-vm-powershell.md).

Uma **versão de imagem** é o que você usa para criar uma VM ao usar uma galeria de imagens compartilhada. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Quando você usa uma versão de imagem para criar uma VM, a versão da imagem é usada para criar discos para a nova VM. Versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você deve ter uma galeria de imagens compartilhada existente. 

Você também deve ter uma VM existente no Azure, na mesma região que a galeria. 

Se a VM tiver um disco de dados anexado, o tamanho do disco de dados não poderá ser maior que 1 TB.

Ao trabalhar com este artigo, substitua os nomes de recursos onde necessário.

## <a name="get-information-about-the-vm"></a>Obter informações sobre a VM

Você pode ver uma lista de VMs que estão disponíveis usando [AZ VM List](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Depois de saber o nome da VM e em qual grupo de recursos ele está, obtenha a ID da VM usando [AZ VM Get-Instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem

Definições de imagem crie um agrupamento lógico para imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criadas dentro delas. 

Os nomes de definição de imagem podem ser compostos de letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos. 

Verifique se a definição da imagem é do tipo correto. Se você tiver generalizado a VM (usando Sysprep para Windows ou waagent-deprovision para Linux), deverá criar uma definição de imagem generalizada usando `--os-state generalized`. Se você quiser usar a VM sem remover contas de usuário existentes, crie uma definição de imagem especializada `--os-state specialized`usando.

Para obter mais informações sobre os valores que você pode especificar para uma definição de imagem, consulte [definições de imagem](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Crie uma definição de imagem na Galeria usando [AZ SIG Image-Definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição da imagem é chamada *myImageDefinition*e é para uma imagem [especializada](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) do SO Linux. Para criar uma definição para imagens usando um sistema operacional Windows, `--os-type Windows`use. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Criar a versão da imagem

Crie uma versão de imagem da VM usando [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

Neste exemplo, a versão da nossa imagem é a *1.0.0* e vamos criar 2 réplicas na região do *Oeste EUA Central* , 1 réplica na região *do Sul EUA Central* e 1 réplica na região *leste dos EUA 2* usando o armazenamento com redundância de zona. As regiões de replicação devem incluir a região em que a VM de origem está localizada.

Substitua o valor de `--managed-image` neste exemplo pela ID da VM da etapa anterior.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Você precisa aguardar que a versão da imagem termine completamente de ser compilada e replicada antes de poder usar a mesma imagem gerenciada para criar outra versão de imagem.
>
> Você também pode armazenar sua imagem no armazenamento Premiun por um armazenamento `--storage-account-type  premium_lrs`com [redundância de zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) ou adição, `--storage-account-type  standard_zrs` adicionando ao criar a versão da imagem.
>

## <a name="next-steps"></a>Próximas etapas

Crie uma VM a partir da [imagem generalizada](vm-generalized-image-version-cli.md) usando o CLI do Azure.
