---
author: baanders
description: incluir arquivo para limpar uma instância dos Gêmeos Digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102245049"
---
* **Caso não precise de nenhum dos recursos criados neste tutorial**, você poderá excluir a instância dos Gêmeos Digitais do Azure e todos os outros recursos deste artigo com o comando [az group delete](/cli/azure/group#az-group-delete). Isso exclui todos os recursos do Azure em um grupo de recursos, bem como o grupo de recursos em si.
    
    > [!IMPORTANT]
    > A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente grupo de recursos ou recursos incorretos.
    
    Abra o [Azure Cloud Shell](https://shell.azure.com) e execute o comando a seguir para excluir o grupo de recursos e tudo o que ele contém.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```