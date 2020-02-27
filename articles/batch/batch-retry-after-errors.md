---
title: Repetindo tarefas após um erro
description: Verifique se há erros e repita-os.
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
ms.openlocfilehash: eda567fda13d6caca679d0ce4947e042eca9530d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652003"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Detectando e manipulando erros do serviço de lote

É importante lembrar-se de verificar se há erros ao trabalhar com uma API de serviço REST. Não é incomum que ocorram erros durante a execução de trabalhos em lotes.

## <a name="common-errors"></a>Erros comuns 

- Falhas de rede-essas são solicitações que nunca atingiram o lote ou a resposta em lote não alcançou o cliente no tempo.
- Erros de servidor interno-essas são respostas HTTP de código de status padrão 5xx.
- A limitação pode causar erros como respostas HTTP de código de status 429 ou 503 com o cabeçalho Retry-After.
- 4xx erros que incluem tais erros como AlreadyExists e InvalidOperation. Isso significa que o recurso não está no estado correto para a transição de estado.

## <a name="when-to-retry"></a>Quando tentar novamente

As APIs do lote o notificarão se houver uma falha. Todos eles podem ser repetidos e todos eles incluem um manipulador de repetição global para essa finalidade. É melhor usar esse mecanismo interno.

Após uma falha, você deve aguardar um pouco (vários segundos entre as repetições) antes de tentar novamente. Se você tentar novamente com muita frequência ou rapidamente, o manipulador de repetição será limitado.


Para obter informações detalhadas sobre cada API e suas políticas de repetição padrão, leia o [status do lote e os códigos de erro](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
