---
author: baanders
description: incluir arquivo para limpar uma instância básica dos Gêmeos Digitais do Azure e o registro de aplicativo
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 45d6a806e94ceca9574b0ed5f73c2b87ef438438
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891481"
---
Se você não precisa mais dos recursos criados neste tutorial, siga estas etapas para excluí-los.

Usando o [Azure Cloud Shell](https://shell.azure.com), exclua todos os recursos do Azure em um grupo de recursos com o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Isso removerá o grupo de recursos e a instância dos Gêmeos Digitais do Azure.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. 

Abra um Azure Cloud Shell e execute o comando a seguir para excluir o grupo de recursos e tudo o que ele contém.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Em seguida, exclua o registro de aplicativo do Azure Active Directory criado para o aplicativo cliente com este comando:

```azurecli-interactive
az ad app delete --id <your-application-ID>
```