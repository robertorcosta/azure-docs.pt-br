---
title: Repetindo tarefas após um erro
description: Verifique se há erros e repita-os.
ms.topic: article
ms.date: 02/15/2020
ms.custom: seodec18
ms.openlocfilehash: 8addc4418f268a2c27b730543bdb309ef45fd5f4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116529"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Detectar e manipular erros do serviço de Lote

É importante lembrar-se de verificar se há erros ao trabalhar com uma API de serviço REST. Não é incomum que ocorram erros durante a execução de trabalhos em lotes.

## <a name="common-errors"></a>Erros comuns 

- Falhas de rede-essas são solicitações que nunca atingiram o lote ou a resposta em lote não alcançou o cliente no tempo.
- Erros de servidor interno-essas são respostas HTTP de código de status padrão 5xx.
- A limitação pode causar erros como respostas HTTP de código de status 429 ou 503 com o cabeçalho Retry-After.
- 4xx erros que incluem tais erros como AlreadyExists e InvalidOperation. Isso significa que o recurso não está no estado correto para a transição de estado.

Para obter informações detalhadas sobre os vários tipos de códigos de erro e códigos de erro específicos, consulte [status do lote e códigos de erro](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Quando tentar novamente

As APIs do lote o notificarão se houver uma falha. Todos eles podem ser repetidos e todos eles incluem um manipulador de repetição global para essa finalidade. É melhor usar esse mecanismo interno.

Após uma falha, você deve aguardar um pouco (vários segundos entre as repetições) antes de tentar novamente. Se você tentar novamente com muita frequência ou rapidamente, o manipulador de repetição será limitado.

### <a name="for-more-information"></a>Para obter mais informações  

Links de [APIs e ferramentas do lote](batch-apis-tools.md) para informações de referência de API. A API do .NET, por exemplo, tem uma [classe RetryPolicyProvider]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) em que a política de repetição necessária deve ser especificada. 

Para obter informações detalhadas sobre cada API e suas políticas de repetição padrão, leia o [status do lote e os códigos de erro](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
