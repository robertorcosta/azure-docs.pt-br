---
author: baanders
description: incluir arquivo para limpar uma instância básica dos Gêmeos Digitais do Azure e o registro de aplicativo
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: e8f167555434338416381aaf8978a3d551686fb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606159"
---
Se você não precisa mais dos recursos criados neste tutorial, siga estas etapas para excluí-los.

Usando o [Azure Cloud Shell](https://shell.azure.com), exclua todos os recursos do Azure em um grupo de recursos com o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Isso removerá o grupo de recursos e a instância dos Gêmeos Digitais do Azure.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. 

Abra um Azure Cloud Shell e execute o comando a seguir para excluir o grupo de recursos e tudo o que ele contém.

```azurecli
az group delete --name <your-resource-group>
```

Em seguida, exclua o registro de aplicativo do Azure Active Directory criado para o aplicativo cliente com este comando:

```azurecli
az ad app delete --id <your-application-ID>
```