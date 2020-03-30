---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474217"
---
Os erros levantados em funções do Azure podem vir de qualquer uma das seguintes origens:

- Uso de [gatilhos e vinculações incorporadas](..\articles\azure-functions\functions-triggers-bindings.md) do Azure
- Chamadas para APIs de serviços subjacentes do Azure
- Chamadas para pontos finais REST
- Chamadas para bibliotecas de clientes, pacotes ou APIs de terceiros

Seguir práticas sólidas de manipulação de erros é importante para evitar a perda de dados ou mensagens perdidas. As práticas recomendadas de manipulação de erros incluem as seguintes ações:

- [Habilitar o Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Use o manuseio estruturado de erros](#use-structured-error-handling)
- [Design para idempotência](../articles/azure-functions/functions-idempotent.md)
- [Implementar políticas de reteste](../articles/azure-functions/functions-reliable-event-processing.md) (quando apropriado)

### <a name="use-structured-error-handling"></a>Use o manuseio estruturado de erros

Capturar e publicar erros é fundamental para monitorar a saúde de sua aplicação. O nível mais alto de qualquer código de função deve incluir um bloco de tentativa/captura. No bloco de captura, você pode capturar e publicar erros.

### <a name="retry-support"></a>Suporte de retentativa

Os seguintes gatilhos têm suporte interno de repetição:

* [Armazenamento de Blobs do Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Armazenamento de fila do Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Barramento de Serviço do Azure (fila/tópico)](../articles/azure-functions/functions-bindings-service-bus.md)

Por padrão, esses gatilhos solicitam novamente solicitações até cinco vezes. Após a quinta tentativa, tanto o armazenamento azure queue e o Azure Service Bus disparam para escrever uma mensagem para uma [fila venenosa](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages).

Você precisa implementar manualmente políticas de repetição para quaisquer outros tipos de gatilhos ou vinculações. Implementações manuais podem incluir a gravação de informações de erro em uma [fila de mensagens venenosas](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs). Ao escrever para uma fila de veneno, você tem a oportunidade de tentar novamente as operações mais tarde. Essa abordagem é a mesma usada pelo gatilho de armazenamento de Blob.
