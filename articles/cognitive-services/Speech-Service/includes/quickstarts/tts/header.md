---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961520"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada. O serviço de conversão de texto em fala fornece inúmeras opções para vozes sintetizadas, em [suporte à linguagem de conversão de texto em fala](../../../language-support.md#text-to-speech). Depois de cumprir alguns pré-requisitos, é necessário seguir apenas 4 etapas para renderizar a fala sintetizada nos locutores padrão:
> [!div class="checklist"]
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Criar um objeto `SpeechSynthesizer` usando o objeto `SpeechConfig` acima.
> * Usando o objeto `SpeechSynthesizer` para falar o texto.
> * Inspecione o `SpeechSynthesisResult` retornado quando a erros.
