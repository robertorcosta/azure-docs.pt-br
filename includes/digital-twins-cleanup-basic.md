---
author: baanders
description: incluir arquivo para limpar uma instância básica dos Gêmeos Digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 2e095a7b286a8860535d6b58fa93098735b30c87
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372303"
---
Se você não precisa mais dos recursos criados neste tutorial, siga estas etapas para excluí-los.

Usando o [Azure Cloud Shell](https://shell.azure.com), exclua todos os recursos do Azure em um grupo de recursos com o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Isso removerá o grupo de recursos e a instância dos Gêmeos Digitais do Azure.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. 

Abra um Azure Cloud Shell e execute o comando a seguir para excluir o grupo de recursos e tudo o que ele contém.

```azurecli
az group delete --name <your-resource-group>
```