---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "81422053"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) e o serviço LUIS (Reconhecimento vocal) para reconhecer intenções em dados de áudio capturados de um microfone. Especificamente, você usará o SDK de Fala para capturar fala e um domínio predefinido do LUIS para identificar intenções de automação doméstica, assim como acender e apagar uma lâmpada. 

Depois de cumprir alguns pré-requisitos, é necessário seguir apenas algumas etapas para identificar intenções por meio de um microfone:

> [!div class="checklist"]
>
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Crie um objeto `IntentRecognizer` usando o objeto `SpeechConfig` acima.
> * Com o objeto `IntentRecognizer`, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o `IntentRecognitionResult` retornado.
