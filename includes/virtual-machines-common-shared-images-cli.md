---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "66814745"
---
## <a name="before-you-begin"></a>Antes de começar

Para concluir o exemplo neste artigo, você precisa ter uma imagem gerenciada existente de uma VM generalizada. Para obter mais informações, consulte [tutorial: criar uma imagem personalizada de uma VM do Azure com o CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Se a imagem gerenciada contiver um disco de dados, o tamanho do disco de dados não poderá ser superior a 1 TB.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Se preferir instalar e usar a CLI localmente, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da Galeria não pode conter traços.   Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

Criar uma galeria de imagens usando [az sig create](/cli/azure/sig#az-sig-create). O exemplo a seguir cria uma galeria chamada *myGallery* em *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem

Definições de imagem crie um agrupamento lógico para imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criadas dentro delas. Os nomes de definição de imagem podem ser compostos de letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos. Para obter mais informações sobre os valores que você pode especificar para uma definição de imagem, consulte [definições de imagem](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Crie uma definição de imagem inicial na galeria usando [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem 

Crie versões da imagem conforme necessário usando [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Você precisará passar a ID da imagem gerenciada para usar como uma linha de base para criar a versão da imagem. Você pode usar [az image list](/cli/azure/image?view#az-image-list) para obter informações sobre as imagens que estão em um grupo de recursos. 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

Neste exemplo, a versão da nossa imagem é a *1.0.0* e vamos criar 2 réplicas na região do *Oeste EUA Central* , 1 réplica na região *do Sul EUA Central* e 1 réplica na região *leste dos EUA 2* usando o armazenamento com redundância de zona.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Você precisa aguardar que a versão da imagem termine completamente de ser compilada e replicada antes de poder usar a mesma imagem gerenciada para criar outra versão de imagem.
>
> Você também pode armazenar todas as réplicas de versão da imagem no [armazenamento com redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) de `--storage-account-type standard_zrs` zona adicionando ao criar a versão da imagem.
>

## <a name="share-the-gallery"></a>Compartilhar a Galeria

Recomendamos que você compartilhe com outros usuários no nível da galeria. Para obter a ID de objeto da galeria, use [AZ SIG show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Use a ID de objeto como um escopo, juntamente com um endereço de email e a [criação de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-create) para conceder a um usuário acesso à galeria de imagens compartilhadas.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


