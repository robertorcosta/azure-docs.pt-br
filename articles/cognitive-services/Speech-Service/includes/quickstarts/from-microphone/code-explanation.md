---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3125b6203f04b4cabd8cd0822a97317185cbf194
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638122"
---
A região e a chave de assinatura do recurso de Fala são necessárias para criar um objeto de configuração de fala. O objeto de configuração é necessário para criar uma instância de um objeto de reconhecedor de fala.

A instância do reconhecedor expõe várias maneiras de reconhecer a fala. Neste exemplo, a fala é reconhecida uma vez. Essa funcionalidade permite que o Serviço de Fala saiba que você está enviando uma só expressão para reconhecimento e permite parar o reconhecimento, assim que a frase é identificada. Depois que o resultado for produzido, o código gravará o motivo do reconhecimento no console.

> [!TIP]
> O SDK de Fala usará como padrão o reconhecimento do uso de `en-us` como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.