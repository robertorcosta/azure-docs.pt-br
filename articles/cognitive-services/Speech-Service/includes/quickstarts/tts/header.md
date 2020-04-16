---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400414"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada. O serviço de conversão de texto em fala fornece inúmeras opções para vozes sintetizadas, em [suporte à linguagem de conversão de texto em fala](../../../language-support.md#text-to-speech). Depois de cumprir alguns pré-requisitos, é necessário seguir apenas 4 etapas para renderizar a fala sintetizada nos locutores padrão:
> [!div class="checklist"]
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Criar um objeto `SpeechSynthesizer` usando o objeto `SpeechConfig` acima.
> * Usando o objeto `SpeechSynthesizer` para falar o texto.
> * Inspecione o `SpeechSynthesisResult` retornado quando a erros.
