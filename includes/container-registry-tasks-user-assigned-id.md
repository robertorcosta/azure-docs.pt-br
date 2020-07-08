---
title: incluir arquivo
description: incluir arquivo
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 012800806aeff81939baa2cee88e78191e4fb6c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195257"
---
### <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo usuário

Crie uma identidade chamada *myACRTasksId* na sua assinatura usando o comando [az identity create][az-identity-create]. Você pode usar o mesmo grupo de recursos que você usou anteriormente para criar um registro de contêiner, ou pode usar um diferente.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Para configurar a identidade atribuída pelo usuário nas etapas a seguir, use o comando [az identity show][az-identity-show] para armazenar a ID do recurso de identidade, a ID da entidade de segurança e a ID do cliente em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show