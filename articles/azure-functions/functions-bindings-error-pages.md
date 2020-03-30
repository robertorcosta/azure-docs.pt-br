---
title: Orientação de manipulação de erros do Azure Functions
description: Aprenda a lidar com erros em funções do Azure com links para erros de vinculação específicos.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586182"
---
# <a name="azure-functions-error-handling"></a>Tratamento de erros do Azure Functions

Erros de manuseio em Funções Azure é importante para evitar dados perdidos, eventos perdidos e monitorar a saúde de sua aplicação.

Este artigo descreve estratégias gerais para o tratamento de erros, juntamente com links para erros específicos de vinculação.

## <a name="handling-errors"></a>Tratando erros

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Códigos de erro de associação

Ao se integrar aos serviços do Azure, os erros podem se originar das APIs dos serviços subjacentes. As informações relativas a erros específicos de vinculação estão disponíveis na seção **Exceções e códigos** de devolução dos seguintes artigos:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento blob](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hubs de Eventos](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT Hubs](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Barramento de Serviço](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Armazenamento de mesa](functions-bindings-storage-table.md#exceptions-and-return-codes)
