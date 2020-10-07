---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "78190988"
---
Atualize *HttpExample\\\_\_init\_\_.py* para que ele corresponda ao código a seguir, adicionando o parâmetro `msg` à definição da função e a `msg.set(name)` na instrução `if name:`.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

O parâmetro `msg` é uma instância do [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). O método `set` grava uma mensagem de cadeia de caracteres na fila, nesse caso, o nome transmitido para a função na cadeia de consulta de URL.
