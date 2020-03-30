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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814745"
---
## <a name="before-you-begin"></a>Antes de começar

Para concluir o exemplo neste artigo, você precisa ter uma imagem gerenciada existente de uma VM generalizada. Para obter mais informações, consulte [Tutorial: Crie uma imagem personalizada de uma VM Azure com o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Se a imagem gerenciada contiver um disco de dados, o tamanho do disco de dados não poderá ser superior a 1 TB.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell [https://shell.azure.com/bash](https://shell.azure.com/bash)em uma guia de navegador separada indo para . Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Se preferir instalar e usar a CLI localmente, consulte [Instalar o Cli zure](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da galeria não pode conter traços.   Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

Criar uma galeria de imagens usando [az sig create](/cli/azure/sig#az-sig-create). O exemplo a seguir cria uma galeria chamada *myGallery* em *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem

Definições de imagem criam um agrupamento lógico para imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criadas dentro delas. Os nomes de definição de imagem podem ser compostos de letras maiúsculas ou minúsculas, dígitos, pontilhados, traços e períodos. Para obter mais informações sobre os valores que você pode especificar para uma definição de imagem, consulte [Definições de imagem](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

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

Neste exemplo, a versão da nossa imagem é *1.0.0* e vamos criar 2 réplicas na região *centro-oeste dos EUA,* 1 réplica na região *centro-sul dos EUA* e 1 réplica na região *leste dos EUA 2* usando armazenamento redundante de zona.


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
> Você precisa esperar que a versão da imagem termine completamente de ser construída e replicada antes de poder usar a mesma imagem gerenciada para criar outra versão de imagem.
>
> Você também pode armazenar todas as réplicas da `--storage-account-type standard_zrs` versão de imagem no Zone Redundant [Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando quando você criar a versão de imagem.
>

## <a name="share-the-gallery"></a>Compartilhe a galeria

Recomendamos que você compartilhe com outros usuários no nível da galeria. Para obter o ID do objeto de sua galeria, use [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Use o ID do objeto como escopo, juntamente com um endereço de e-mail e [a atribuição de função az criar](/cli/azure/role/assignment#az-role-assignment-create) para dar ao usuário acesso à galeria de imagens compartilhadas.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


