---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "66226041"
---
## <a name="using-rbac-to-share-images"></a>Usar o RBAC para compartilhar imagens

Você pode compartilhar imagens entre assinaturas usando o RBAC (controle de acesso baseado em função). Qualquer usuário que tenha permissões de leitura para uma versão de imagem, mesmo entre assinaturas, poderá implantar uma máquina virtual usando a versão da imagem.

Para obter mais informações de como compartilhar recursos usando o RBAC, confira [Gerenciar o acesso usando a CLI do Azure e RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Listar informações

Obtenha informações de local, status e outras sobre as galerias de imagens disponíveis usando [az sig list](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Liste as definições de imagem em uma galeria, incluindo informações sobre o tipo e o status do sistema operacional, usando [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Liste as versões da imagem compartilhada em uma galeria usando [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Obtenha a ID de uma versão de imagem usando [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```