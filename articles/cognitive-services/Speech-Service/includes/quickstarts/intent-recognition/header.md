---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900412"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) e o serviço LUIS (Reconhecimento vocal) para reconhecer intenções em dados de áudio capturados de um microfone. Especificamente, você usará o SDK de Fala para capturar fala e um domínio predefinido do LUIS para identificar intenções de automação doméstica, assim como acender e apagar uma lâmpada. 

Depois de cumprir alguns pré-requisitos, é necessário seguir apenas algumas etapas para identificar intenções por meio de um microfone:

> [!div class="checklist"]
>
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Crie um objeto `IntentRecognizer` usando o objeto `SpeechConfig` acima.
> * Com o objeto `IntentRecognizer`, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o `IntentRecognitionResult` retornado.
