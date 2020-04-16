---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400710"
---
A região e a chave de assinatura do recurso de Fala são necessárias para criar um objeto de configuração de fala. O objeto de configuração é necessário para criar uma instância de um objeto de reconhecedor de fala.

A instância do reconhecedor expõe várias maneiras de reconhecer a fala. Neste exemplo, a fala é reconhecida uma vez. Essa funcionalidade permite que o Serviço de Fala saiba que você está enviando uma só expressão para reconhecimento e permite parar o reconhecimento, assim que a frase é identificada. Depois que o resultado for produzido, o código gravará o motivo do reconhecimento no console.

> [!TIP]
> O SDK de Fala usará como padrão o reconhecimento do uso de `en-us` como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.