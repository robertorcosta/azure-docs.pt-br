---
title: Compartilhe imagens da galeria entre os inquilinos no Azure
description: Aprenda a compartilhar imagens de VM entre os inquilinos do Azure usando galerias de imagens compartilhadas.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 4259ca01dbe45463b73cf1ec1c620c3921ab9459
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758474"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Compartilhar imagens de VM da galeria entre os inquilinos do Azure

Galerias de imagens compartilhadas permitem compartilhar imagens usando o RBAC. Você pode usar o RBAC para compartilhar imagens dentro do seu inquilino e até mesmo para indivíduos fora do seu inquilino. Para obter mais informações sobre esta opção de compartilhamento simples, consulte o [Share da galeria](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Você não pode usar o portal para implantar uma VM de uma imagem em outro inquilino azul. Para criar uma VM a partir de uma imagem compartilhada entre os inquilinos, você deve usar o Azure CLI ou [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Crie uma VM usando o Azure CLI

Faça login no principal de serviço para o inquilino 1 usando o appID, a chave do aplicativo e o ID do inquilino 1. Você pode `az account show --query "tenantId"` usar para obter as ids inquilino, se necessário.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Faça login no principal de serviço para o inquilino 2 usando o appID, a chave do aplicativo e o ID do inquilino 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Crie a VM. Substitua as informações no exemplo pelas suas.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Próximas etapas

Se você tiver algum problema, você poderá [solucionar problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).