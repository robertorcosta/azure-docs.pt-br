---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 03ed1b28b4cabc054301e11c2b4d0f9e632abe02
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839048"
---
É possível usar o comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) para exibir as Filas de armazenamento em sua conta, como no exemplo a seguir:

```azurecli-interactive
az storage queue list --output tsv
```

A saída desse comando inclui uma fila denominada `outqueue`, que é a fila que foi criada quando a função foi executada.

Em seguida, use o comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) para exibir as mensagens nessa fila, como no exemplo a seguir:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A cadeia de caracteres retornada deve ser a mesma que a mensagem enviada para testar a função.

> [!NOTE]  
> O exemplo anterior decodifica a cadeia de caracteres retornada de base64. Isso ocorre porque as associações de Armazenamento de fila gravam e leem do Armazenamento do Azure como [cadeias de caracteres base64](../articles/azure-functions/functions-bindings-storage-queue.md#encoding).