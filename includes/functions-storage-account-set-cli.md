---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839114"
---
Abra o arquivo local.settings.json e copie o valor de `AzureWebJobsStorage`, que é a cadeia de conexão da Conta de armazenamento. Defina a variável de ambiente `AZURE_STORAGE_CONNECTION_STRING` como a cadeia de conexão usando o seguinte comando do Bash:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Quando você definir a cadeia de conexão na variável de ambiente `AZURE_STORAGE_CONNECTION_STRING`, é possível acessar a conta de Armazenamento sem ter que fornecer a autenticação toda vez.