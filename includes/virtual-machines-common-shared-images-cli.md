---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: de1a22ed6e9707767c0d097a9250f0bdd31414d5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788953"
---
## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. 

Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da Galeria não pode conter traços.   Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

Criar uma galeria de imagens usando [az sig create](/cli/azure/sig#az-sig-create). O exemplo a seguir cria um grupo de recursos chamado Galeria chamado *myGalleryRG* no *leste dos EUA*e uma galeria chamada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Compartilhar a Galeria

Você pode compartilhar imagens entre assinaturas usando o RBAC (controle de acesso baseado em função). Você pode compartilhar imagens na Galeria, definição de imagem ou versão de imagem leve. Qualquer usuário que tenha permissões de leitura para uma versão de imagem, mesmo entre assinaturas, poderá implantar uma VM usando a versão da imagem.

Recomendamos que você compartilhe com outros usuários no nível da galeria. Para obter a ID de objeto da galeria, use [AZ SIG show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Use a ID de objeto como um escopo, juntamente com um endereço de email e a [criação de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-create) para conceder a um usuário acesso à galeria de imagens compartilhadas. Substitua `<email-address>` e `<gallery iD>` pelas suas próprias informações.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Para obter mais informações de como compartilhar recursos usando o RBAC, confira [Gerenciar o acesso usando a CLI do Azure e RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).
