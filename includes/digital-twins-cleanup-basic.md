---
author: baanders
description: incluir arquivo para limpar uma instância básica dos Gêmeos Digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011230"
---
Se você não precisa mais dos recursos criados neste tutorial, siga estas etapas para excluí-los.

Usando o [Azure Cloud Shell](https://shell.azure.com), você pode excluir todos os recursos do Azure em um grupo de recursos com o comando [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete). Esse comando removerá o grupo de recursos e a instância dos Gêmeos Digitais do Azure.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente grupo de recursos ou recursos incorretos.

Abra o Azure Cloud Shell e execute o comando a seguir para excluir o grupo de recursos e tudo o que ele contém.

```azurecli-interactive
az group delete --name <your-resource-group>
```