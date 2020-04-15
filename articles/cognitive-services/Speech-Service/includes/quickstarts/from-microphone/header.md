---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3775690802c89805ccf9df1ee6d6717a8818213f
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659457"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de maneira interativa a fala de uma entrada de microfone e obter a transcrição de texto do áudio capturado. É fácil integrar esse recurso a seus aplicativos ou dispositivos para tarefas de reconhecimento comuns, como transcrever conversas. Ele também pode ser usado para integrações mais complexas, como usar o Bot Framework com o SDK de Fala para criar assistentes de voz.

Depois de cumprir alguns pré-requisitos, é necessário seguir apenas quatro etapas para reconhecer a fala:

> [!div class="checklist"]
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Criar um objeto `SpeechRecognizer` usando o objeto `SpeechConfig` acima.
> * Com o objeto `SpeechRecognizer`, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o `SpeechRecognitionResult` retornado.
