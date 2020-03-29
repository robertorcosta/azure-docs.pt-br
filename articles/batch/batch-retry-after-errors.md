---
title: Rejulgando tarefas após um erro
description: Verifique se há erros e tente novamente.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919984"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Detectando e manipulando erros de serviço em lote

É importante lembrar de verificar se há erros ao trabalhar com uma API de serviço REST. Não é incomum que erros ocorram ao executar trabalhos em lote.

## <a name="common-errors"></a>Erros comuns 

- Falhas de rede - são solicitações que nunca chegaram ao Batch ou a resposta batch não chegou ao cliente a tempo.
- Erros internos do servidor - estes são a resposta HTTP do código de status padrão 5xx.
- O estrangulamento pode causar erros, como 429 ou 503 respostas HTTP do código de status com o cabeçalho Retry-after.
- 4xx erros que incluem erros como JáExiste e InvalidOperation. Isso significa que o recurso não está no estado correto para a transição estadual.

Para obter informações detalhadas sobre os vários tipos de códigos de erro e códigos de erro específicos, consulte [Status do lote e códigos de erro](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Quando tentar novamente

As APIs do lote irão notificá-lo se houver uma falha. Todos eles podem ser julgados novamente, e todos eles incluem um manipulador de repetição global para esse fim. É melhor usar este mecanismo embutido.

Depois de uma falha, você deve esperar um pouco (vários segundos entre tentativas) antes de tentar novamente. Se você tentar de forma muito freqüente ou muito rápida, o manipulador de repetição irá acelerar.

### <a name="for-more-information"></a>Para obter mais informações  

[APIs em lote e links de ferramentas](batch-apis-tools.md) para informações de referência da API. A API .NET, por exemplo, tem uma [classe RetryPolicyProvider]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) onde a política de repetição necessária deve ser especificada. 

Para obter informações detalhadas sobre cada API e suas políticas de repetição padrão, leia [Status do lote e códigos de erro](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
