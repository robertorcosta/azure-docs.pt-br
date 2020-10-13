---
title: Compartilhar imagens da Galeria entre locatários
description: Saiba como compartilhar imagens de VM em locatários do Azure usando galerias de imagens compartilhadas usando exemplos do Linux.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: f6ffa23818a223ef1c0d46823955668ad96292d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307204"
---
# <a name="share-gallery-vm-images-across-azure-tenants---linux-examples"></a>Compartilhar imagens de VM da galeria em locatários do Azure – exemplos do Linux

As galerias de imagens compartilhadas permitem compartilhar imagens usando o RBAC. Você pode usar o RBAC para compartilhar imagens dentro de seu locatário e até mesmo para indivíduos fora do seu locatário. Para obter mais informações sobre essa opção de compartilhamento simples, consulte [compartilhar a Galeria](./shared-images-portal.md#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Você não pode usar o portal para implantar uma VM de uma imagem em outro locatário do Azure. Para criar uma VM de uma imagem compartilhada entre locatários, você deve usar o CLI do Azure ou o [PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Criar uma VM usando CLI do Azure

Conecte a entidade de serviço para o locatário 1 usando a appID, a chave do aplicativo e a ID do locatário 1. Você pode usar `az account show --query "tenantId"` para obter as IDs de locatário, se necessário.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Conecte a entidade de serviço para o locatário 2 usando a appID, a chave do aplicativo e a ID do locatário 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Crie a VM. Substitua as informações no exemplo pelo seu próprio.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Próximas etapas

Se você tiver algum problema, você poderá [solucionar problemas de galerias de imagens compartilhadas](../troubleshooting-shared-images.md).
