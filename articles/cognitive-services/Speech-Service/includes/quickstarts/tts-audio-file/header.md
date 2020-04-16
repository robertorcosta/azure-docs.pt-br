---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400306"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada em um arquivo de áudio. O serviço de conversão de texto em fala fornece inúmeras opções para vozes sintetizadas, em [suporte à linguagem de conversão de texto em fala](../../../language-support.md#text-to-speech). Depois de cumprir alguns pré-requisitos, é necessário seguir apenas cinco etapas para sintetizar a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Criar um objeto de Configuração de Áudio que especifique o nome do arquivo .WAV.
> * Criar um objeto `SpeechSynthesizer` usando os objetos de configuração acima.
> * Usando o objeto `SpeechSynthesizer`, converter o texto em fala sintetizada, salvando-o no arquivo de áudio especificado.
> * Inspecionar o `SpeechSynthesizer` que retornar para verificar se não há erros.
