---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839063"
---
Você já criou um aplicativo de funções no Azure, juntamente com a conta de armazenamento necessária. A cadeia de conexão dessa conta é armazenada com segurança nas configurações do aplicativo no Azure. Neste artigo, você escreverá mensagens em uma Fila de armazenamento na mesma conta. Para se conectar à sua Conta de armazenamento ao executar a função localmente, é necessário baixar as configurações do aplicativo para o arquivo local.settings.json. Execute o comando do Azure Functions Core Tools a seguir para baixar as configurações para local.settings.json, substituindo `<APP_NAME>` pelo nome do seu aplicativo de funções do artigo anterior:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Talvez você precise entrar em sua conta do Azure.

> [!IMPORTANT]  
> Como ela contém segredos, o arquivo local.settings.json nunca é publicado e deve ser excluído do controle do código-fonte.

É necessário ter o valor `AzureWebJobsStorage`, que é a cadeia de conexão da Conta de armazenamento. Use esta conexão para verificar se a associação de saída funciona conforme o esperado.
