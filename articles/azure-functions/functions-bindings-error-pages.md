---
title: Diretrizes de tratamento de erro Azure Functions
description: Saiba como tratar erros no Azure Functions com links para erros de associação específicos.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77586182"
---
# <a name="azure-functions-error-handling"></a>Tratamento de erros do Azure Functions

O tratamento de erros no Azure Functions é importante para evitar perda de dados, eventos perdidos e para monitorar a integridade do aplicativo.

Este artigo descreve estratégias gerais para tratamento de erros junto com links para erros específicos de associação.

## <a name="handling-errors"></a>Tratando erros

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Códigos de erro de associação

Ao integrar com os serviços do Azure, os erros podem se originar das APIs dos serviços subjacentes. Informações relacionadas a erros específicos de associação estão disponíveis na seção **exceções e códigos de retorno** dos seguintes artigos:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de Blobs](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hubs de Evento](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Hubs IoT](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Barramento de Serviço](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table.md#exceptions-and-return-codes)
