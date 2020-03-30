---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961438"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada em um arquivo de áudio. O serviço de conversão de texto em fala fornece inúmeras opções para vozes sintetizadas, em [suporte à linguagem de conversão de texto em fala](../../../language-support.md#text-to-speech). Depois de cumprir alguns pré-requisitos, é necessário seguir apenas cinco etapas para sintetizar a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Criar um objeto de Configuração de Áudio que especifique o nome do arquivo .WAV.
> * Criar um objeto `SpeechSynthesizer` usando os objetos de configuração acima.
> * Usando o objeto `SpeechSynthesizer`, converter o texto em fala sintetizada, salvando-o no arquivo de áudio especificado.
> * Inspecionar o `SpeechSynthesizer` que retornar para verificar se não há erros.
